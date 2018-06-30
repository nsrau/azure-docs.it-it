---
title: 'Servizio app di Azure stack: aggiornamento del dominio di errore | Documenti Microsoft'
description: Come ridistribuire il servizio App di Azure nello Stack di Azure nei domini di errore
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
ms.openlocfilehash: ce57e153dcab6a386150ebefe1ecb4a018514247
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/29/2018
ms.locfileid: "37130371"
---
# <a name="how-to-redistribute-azure-app-service-on-azure-stack-across-fault-domains"></a>Come ridistribuire il servizio App di Azure nello Stack di Azure nei domini di errore

*Si applica a: Azure Stack integrati sistemi*

Con l'aggiornamento 1802, Stack di Azure supporta ora la distribuzione dei carichi di lavoro nei domini di errore, una funzionalità che è fondamentale per la disponibilità elevata.

>[!IMPORTANT]
>Per sfruttare i vantaggi del supporto di domini di errore, è necessario aggiornare il sistema Azure Stack integrato a 1802. Questo documento si applica solo alle distribuzioni di provider di risorse del servizio App completati prima dell'aggiornamento 1802. Se è stato distribuito il servizio App nello Stack di Azure dopo che è stato applicato l'aggiornamento 1802 allo Stack di Azure, il provider di risorse è già distribuito nei domini di errore.

## <a name="rebalance-an-app-service-resource-provider-across-fault-domains"></a>Ribilanciare un provider di risorse di servizio App nei domini di errore

Per ridistribuire i set di scalabilità per il provider di risorse di servizio App distribuiti, è necessario eseguire i passaggi in questo articolo per ogni set di scalabilità. Per impostazione predefinita, i nomi scaleset sono:

* ManagementServersScaleSet
* FrontEndsScaleSet
* PublishersScaleSet
* SharedWorkerTierScaleSet
* SmallWorkerTierScaleSet
* MediumWorkerTierScaleSet
* LargeWorkerTierScaleSet

>[!NOTE]
> Se non si dispone di istanze distribuite in alcuni set di scalabilità di livello il lavoro, è necessario ribilanciare i set di scalabilità. I set di scalabilità verranno bilanciati correttamente quando si aumenta il essere adattati in futuro.

Per ampliare il set di scalabilità, seguire questi passaggi:

1. Accedi al portale di amministrazione di Stack di Azure.
2. Selezionare **Altri servizi**.
3. CALCOLO, selezionare **set di scalabilità di macchine virtuali**. Verranno elencati i set di scalabilità esistenti distribuiti come parte della distribuzione del servizio App con le informazioni sul conteggio di istanza. Nella schermata seguente mostra un esempio di set di scalabilità.

      ![Set di scalabilità di servizio Azure App elencati nella macchina virtuale scala set UX][1]

4. Scalabilità orizzontale ogni set. Ad esempio, se si dispongano di tre istanze esistenti nel set di scalabilità prevedono la scalabilità orizzontale a 6 in modo che le tre nuove istanze vengono distribuite in domini di errore. Nell'esempio di PowerShell seguente mostra per scalare in orizzontale il set di scalabilità.

   ```powershell
   Add-AzureRmAccount -EnvironmentName AzureStackAdmin 

   # Get current scale set
   $vmss = Get-AzureRmVmss -ResourceGroupName "AppService.local" -VMScaleSetName "SmallWorkerTierScaleSet"

   # Set and update the capacity of your scale set
   $vmss.sku.capacity = 6
   Update-AzureRmVmss -ResourceGroupName AppService.local" -Name "SmallWorkerTierScaleSet" -VirtualMachineScaleSet $vmss
   ```

   >[!NOTE]
   >Questo passaggio può richiedere diverse ore per terminare, a seconda del tipo di ruolo e il numero di istanze.

5. In **ruoli di amministrazione del servizio App**, monitorare lo stato delle nuove istanze del ruolo. Per controllare lo stato di un'istanza del ruolo, selezionare il tipo di ruolo nell'elenco

    ![Servizio App di Azure su ruoli di Azure Stack][2]

6. Quando lo stato delle nuove istanze del ruolo è **pronto**, tornare alla **Set di scalabilità della macchina virtuale** e **eliminare** le istanze del ruolo precedente.

7. Ripetere questi passaggi per **ogni** set di scalabilità della macchina virtuale.

## <a name="next-steps"></a>Passaggi successivi

È anche possibile provare altre [piattaforma distribuita come un servizio (PaaS) di servizi](azure-stack-tools-paas-services.md).

* [Provider di risorse di SQL Server](azure-stack-sql-resource-provider-deploy.md)
* [Provider di risorse MySQL](azure-stack-mysql-resource-provider-deploy.md)

<!--Image references-->
[1]: ./media/azure-stack-app-service-fault-domain-update/app-service-scale-sets.png
[2]: ./media/azure-stack-app-service-fault-domain-update/app-service-roles.png
