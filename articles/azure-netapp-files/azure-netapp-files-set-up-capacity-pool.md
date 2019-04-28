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
ms.topic: conceptual
ms.date: 02/15/2019
ms.author: b-juche
ms.openlocfilehash: 8f50b2ad34c705c8d3831d8243f136c41d750dc0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60691099"
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
     Specificare il livello di servizio per il pool di capacità: [**Premium**](azure-netapp-files-service-levels.md#Premium) o [**Standard**](azure-netapp-files-service-levels.md#Standard).

   * **Dimensioni**     
     Specificare le dimensioni del pool di capacità da acquistare.        
     Le dimensioni minime del pool di capacità sono di 4 TiB. È possibile creare un pool con dimensioni in multipli di 4 TiB.   
      
     ![Nuovo pool di capacità](../media/azure-netapp-files/azure-netapp-files-new-capacity-pool.png)

4. Fare clic su **OK**.

## <a name="next-steps"></a>Passaggi successivi 

- [Livelli di servizio per Azure NetApp Files](azure-netapp-files-service-levels.md)
- Vedere la [pagina dei prezzi di Azure NetApp Files](https://azure.microsoft.com/pricing/details/storage/netapp/) per il prezzo dei diversi livelli di servizio
- [Delegare una subnet ad Azure NetApp Files](azure-netapp-files-delegate-subnet.md)
