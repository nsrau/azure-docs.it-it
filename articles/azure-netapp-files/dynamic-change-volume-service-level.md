---
title: Modificare dinamicamente il livello di servizio di un volume per Azure NetApp Files | Microsoft Docs
description: Viene descritto come modificare dinamicamente il livello di servizio di un volume.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/12/2020
ms.author: b-juche
ms.openlocfilehash: e5219e1c87221ade8da68c21209f41b4d6139be2
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94579080"
---
# <a name="dynamically-change-the-service-level-of-a-volume"></a>Modificare dinamicamente il livello di servizio di un volume

> [!IMPORTANT] 
> * La registrazione dell'anteprima pubblica per questa funzionalità è in attesa fino a un ulteriore preavviso. 
> * La modifica dinamica del livello di servizio di un volume di destinazione della replica non è attualmente supportata.

È possibile modificare il livello di servizio di un volume esistente spostando il volume in un altro pool di capacità che utilizza il [livello di servizio](azure-netapp-files-service-levels.md) desiderato per il volume. Questa modifica sul posto del livello di servizio per il volume non richiede la migrazione dei dati. Non influisca inoltre sull'accesso al volume.  

Questa funzionalità consente di soddisfare le esigenze del carico di lavoro su richiesta.  È possibile modificare un volume esistente per usare un livello di servizio superiore per ottenere prestazioni migliori oppure per usare un livello di servizio inferiore per l'ottimizzazione dei costi. Ad esempio, se il volume si trova attualmente in un pool di capacità che usa il livello di servizio *standard* e si vuole che il volume usi il livello di servizio *Premium* , è possibile spostare il volume in modo dinamico in un pool di capacità che usa il livello di servizio *Premium* .  

Il pool di capacità in cui si desidera spostare il volume deve essere già esistente. Il pool di capacità può contenere altri volumi.  Se si vuole spostare il volume in un nuovo pool di capacità, è necessario [creare il pool di capacità](azure-netapp-files-set-up-capacity-pool.md) prima di spostare il volume.  

## <a name="considerations"></a>Considerazioni

* Dopo che il volume è stato spostato in un altro pool di capacità, non sarà più possibile accedere ai log attività e alle metriche del volume precedenti. Il volume inizierà con i nuovi log attività e le metriche nel nuovo pool di capacità.

* Se si sposta un volume in un pool di capacità di un livello di servizio superiore (ad esempio, passando da *standard* a *Premium* o livello di servizio *ultra* ), è necessario attendere almeno sette giorni prima di poter spostare *nuovamente* il volume in un pool di capacità di un livello di servizio inferiore (ad esempio, passando da *ultra* a *Premium* o *standard* ).  
<!-- 
## Register the feature

The feature to move a volume to another capacity pool is currently in preview. If you are using this feature for the first time, you need to register the feature first.

1. Register the feature: 

    ```azurepowershell-interactive
    Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFTierChange
    ```

2. Check the status of the feature registration: 

    > [!NOTE]
    > The **RegistrationState** may be in the `Registering` state for up to 60 minutes before changing to`Registered`. Wait until the status is **Registered** before continuing.

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFTierChange
    ```
You can also use [Azure CLI commands](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) `az feature register` and `az feature show` to register the feature and display the registration status. 
--> 
## <a name="move-a-volume-to-another-capacity-pool"></a>Spostare un volume in un altro pool di capacità

1.  Nella pagina volumi, fare clic con il pulsante destro del mouse sul volume di cui si desidera modificare il livello di servizio. Selezionare **Cambia pool**.

    ![Pulsante destro del mouse su volume](../media/azure-netapp-files/right-click-volume.png)

2. Nella finestra Cambia pool selezionare il pool di capacità in cui si desidera spostare il volume. 

    ![Cambia pool](../media/azure-netapp-files/change-pool.png)

3.  Fare clic su **OK**.


## <a name="next-steps"></a>Passaggi successivi  

* [Livelli di servizio per Azure NetApp Files](azure-netapp-files-service-levels.md)
* [Configurare un pool di capacità](azure-netapp-files-set-up-capacity-pool.md)
