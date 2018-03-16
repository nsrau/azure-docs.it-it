---
title: 'Servizio app di Azure stack: aggiornamento del dominio di errore | Documenti Microsoft'
description: Come ridistribuire il servizio App di Azure nello Stack di Azure nei domini di errore
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/09/2018
ms.author: anwestg
ms.openlocfilehash: 851747263879aa89fabe8b168876238a004ea8b2
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="how-to-redistribute-azure-app-service-on-azure-stack-across-fault-domains"></a>Come ridistribuire il servizio App di Azure nello Stack di Azure nei domini di errore

*Si applica a: Azure Stack integrate di sistemi*

Con l'aggiornamento 1802, Stack di Azure supporta ora la distribuzione dei carichi di lavoro nei domini di errore, una funzionalità, è fondamentale per la disponibilità elevata.

> [!IMPORTANT]
> Deve avere il sistema è aggiornato Stack Azure integrato a 1802 per essere in grado di sfruttare i vantaggi del supporto di domini di errore.  Questo documento si applica solo alle distribuzioni di provider di risorse servizio App che sono state completate, prima dell'aggiornamento 1802.  Se è stato distribuito il servizio App nello Stack di Azure dopo che è stato applicato l'aggiornamento 1802 allo Stack di Azure, il provider di risorse è già distribuito nei domini di errore.
>
>

## <a name="rebalance-an-app-service-resource-provider-across-fault-domains"></a>Ribilanciare un provider di risorse di servizio App nei domini di errore

Per ridistribuire i set di scalabilità per il provider di risorse di servizio App distribuiti, è necessario eseguire i passaggi seguenti per ogni set di scalabilità.  Per impostazione predefinita i nomi scaleset sono:

* ManagementServersScaleSet
* FrontEndsScaleSet
* PublishersScaleSet
* SharedWorkerTierScaleSet
* SmallWorkerTierScaleSet
* MediumWorkerTierScaleSet
* LargeWorkerTierScaleSet

> [!NOTE]
> Se non si dispone di alcun istanze distribuite in alcuni set di scalabilità di livello il lavoro, è necessario ribilanciare i set di scalabilità.  I set di scalabilità verranno bilanciati correttamente quando si aumenta il essere adattati in futuro.
>
>

1. Selezionare i set di scalabilità macchina virtuale nel portale di amministrazione di Stack di Azure.  Verranno elencati i set di scalabilità esistenti distribuiti come parte della distribuzione del servizio App con le informazioni sul conteggio di istanza.

    ![Set di scalabilità di servizio Azure App elencati nella macchina virtuale scala set UX][1]

2. Avanti con scalabilità orizzontale ogni set.  Ad esempio, se si dispongano di tre istanze esistenti nel set di scalabilità prevedono la scalabilità orizzontale a 6 in modo che le tre nuove istanze verranno eseguito il provisioning in domini di errore.
    a. [Configurare l'ambiente Azure Stack Admin in PowerShell](azure-stack-powershell-configure-admin.md) b. Usare questo esempio per scalare orizzontalmente il set di scalabilità:
        ```powershell
                Login-AzureRMAccount -EnvironmentName AzureStackAdmin 

                # Get current scale set
                $vmss = Get-AzureRmVmss -ResourceGroupName "AppService.local" -VMScaleSetName "SmallWorkerTierScaleSet"

                # Set and update the capacity of your scale set
                $vmss.sku.capacity = 6
                Update-AzureRmVmss -ResourceGroupName "AppService.local" -Name "SmallWorkerTierScaleSet" -VirtualMachineScaleSet $vmss
        '''
> [!NOTE]
> Questo passaggio può richiedere un numero di ore in base al tipo di ruolo e il numero di istanze.
>
>

3. Monitorare lo stato delle istanze del ruolo nuovo nel pannello dei ruoli di amministrazione del servizio App.  Controllare lo stato di una singola istanza del ruolo selezionando il tipo di ruolo nell'elenco

    ![Servizio App di Azure su ruoli di Azure Stack][2]

4. Un nuove istanze si trovano in un **pronto** stato, tornare al pannello del Set di scalabilità della macchina virtuale e **eliminare** le istanze del precedente.

5. Ripetere questi passaggi per **ogni** set di scalabilità della macchina virtuale.

## <a name="next-steps"></a>Passaggi successivi

È anche possibile provare altre [piattaforma come un servizio (PaaS) di servizi](azure-stack-tools-paas-services.md).

* [Provider di risorse di SQL Server](azure-stack-sql-resource-provider-deploy.md)
* [Provider di risorse MySQL](azure-stack-mysql-resource-provider-deploy.md)

<!--Image references-->
[1]: ./media/azure-stack-app-service-fault-domain-update/app-service-scale-sets.png
[2]: ./media/azure-stack-app-service-fault-domain-update/app-service-roles.png
