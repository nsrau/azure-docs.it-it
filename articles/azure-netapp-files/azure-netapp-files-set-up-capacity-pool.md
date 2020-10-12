---
title: Configurare un pool di capacità per Azure NetApp Files | Microsoft Docs
description: Descrive come configurare un pool di capacità in modo da poter creare volumi al suo interno.
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
ms.date: 09/22/2020
ms.author: b-juche
ms.openlocfilehash: 2b52ad50854092cddd7b9e79cbeebd4a83017081
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91325412"
---
# <a name="set-up-a-capacity-pool"></a>Configurare un pool di capacità

La configurazione di un pool di capacità consente di creare volumi al suo interno.  

## <a name="before-you-begin"></a>Prima di iniziare 

È necessario avere già creato un account di NetApp.   

[Creare un account di NetApp](azure-netapp-files-create-netapp-account.md)

## <a name="steps"></a>Passaggi 

1. Passare al pannello di gestione per l'account di NetApp e quindi selezionare **Pool di capacità** nel riquadro di spostamento.  
    
    ![Passare al pool di capacità](../media/azure-netapp-files/azure-netapp-files-navigate-to-capacity-pool.png)

2. Fare clic su **+ Aggiungi pool** per creare un nuovo pool di capacità.   
    Verrà visualizzata la finestra New Capacity Pool (Nuovo pool di capacità).

3. Specificare le informazioni seguenti per il nuovo pool di capacità:  
   * **Nome**  
     Specificare il nome per il pool di capacità.  
     Il nome del pool di capacità deve essere univoco per ogni account di NetApp.

   * **Livello di servizio**   
     Questo campo mostra le prestazioni di destinazione per il pool di capacità.  
     Specificare il livello di servizio per il pool di capacità: [**ultra**](azure-netapp-files-service-levels.md#Ultra), [**Premium**](azure-netapp-files-service-levels.md#Premium)o [**standard**](azure-netapp-files-service-levels.md#Standard).

    * **Dimensioni**     
     Specificare le dimensioni del pool di capacità da acquistare.        
     Le dimensioni minime del pool di capacità sono di 4 TiB. È possibile creare un pool con dimensioni in multipli di 4 TiB.   

   * **QoS**   
     Specificare se il pool di capacità deve usare il tipo di QoS **manuale** o **automatico** .  

     Per informazioni sui tipi QoS, vedere [considerazioni sulle prestazioni](azure-netapp-files-performance-considerations.md) e sulla [gerarchia di archiviazione](azure-netapp-files-understand-storage-hierarchy.md) .  

     > [!IMPORTANT] 
     > L'impostazione del **tipo QoS** su **manuale** è permanente. Non è possibile convertire un pool di capacità QoS manuale per usare la funzionalità QoS automatica. Tuttavia, è possibile convertire un pool di capacità QoS automatica per usare la funzionalità QoS manuale. Vedere [modificare un pool di capacità per usare QoS manuale](manage-manual-qos-capacity-pool.md#change-to-qos).   
     > Per usare il tipo QoS manuale per un pool di capacità è necessaria la registrazione. Vedere [Gestire un pool di capacità QoS manuale](manage-manual-qos-capacity-pool.md#register-the-feature). 

    ![Nuovo pool di capacità](../media/azure-netapp-files/azure-netapp-files-new-capacity-pool.png)

4. Fare clic su **Crea**.

## <a name="next-steps"></a>Passaggi successivi 

- [Gerarchia di archiviazione](azure-netapp-files-understand-storage-hierarchy.md) 
- [Livelli di servizio per Azure NetApp Files](azure-netapp-files-service-levels.md)
- [Pagina dei prezzi di Azure NetApp Files](https://azure.microsoft.com/pricing/details/storage/netapp/)
- [Gestire un pool di capacità QoS manuale](manage-manual-qos-capacity-pool.md)
- [Delegare una subnet ad Azure NetApp Files](azure-netapp-files-delegate-subnet.md)
