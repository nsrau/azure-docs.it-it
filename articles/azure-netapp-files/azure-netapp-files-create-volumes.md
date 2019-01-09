---
title: Creare un volume per Azure NetApp Files | Microsoft Docs
description: Descrive come creare un volume per Azure NetApp Files.
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
ms.topic: get-started-article
ms.date: 12/17/2018
ms.author: b-juche
ms.openlocfilehash: 937ff9cf4c099f229df28070be07ba76339704e0
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2018
ms.locfileid: "53584004"
---
# <a name="create-a-volume-for-azure-netapp-files"></a>Creare un volume per Azure NetApp Files

L'utilizzo della capacità di un volume concorre al calcolo della capacità di cui è stato effettuato il provisioning del pool.  È possibile creare più volumi in un pool di capacità, ma l'utilizzo totale della capacità dei volumi non deve superare le dimensioni del pool. 

## <a name="before-you-begin"></a>Prima di iniziare 
È necessario avere già configurato un pool di capacità.   
[Configurare un pool di capacità](azure-netapp-files-set-up-capacity-pool.md)   
È necessario delegare una subnet ad Azure NetApp Files.  
[Delegare una subnet ad Azure NetApp Files](azure-netapp-files-delegate-subnet.md)


## <a name="steps"></a>Passaggi 
1.  Scegliere il pannello **Volumi** nel pannello Manage Capacity Pools (Gestisci pool di capacità). 
2.  Fare clic su **+ Aggiungi volume** per creare un volume.  
    Verrà visualizzata la finestra Nuovo volume.

3.  Nella finestra Nuovo volume fare clic su **Crea** e specificare le informazioni per i campi seguenti:   
    * **Nome**      
        Specificare il nome per il volume che si sta creando.   

        Il nome deve essere univoco all'interno di un gruppo di risorse. Deve essere composto da almeno tre caratteri.  È possibile usare qualsiasi carattere alfanumerico.

    * **Percorso file**  
        Specificare il percorso del file che verrà usato per creare il percorso di esportazione per il nuovo volume. Il percorso di esportazione viene usato per montare il volume e accedervi.   
     
        Il nome di percorso di file può contenere solo lettere, numeri e trattini ("-"). Il nome deve avere una lunghezza compresa tra 16 e 40 caratteri.  

    * **Quota**  
        Specificare la quantità di spazio di archiviazione logico allocato al volume.  

        Il campo **Quota disponibile** mostra la quantità di spazio inutilizzato nel pool di capacità scelto che è possibile usare per la creazione di un nuovo volume. Le dimensioni del nuovo volume non devono superare la quota disponibile.  

    * **Rete virtuale**  
        Specificare la rete virtuale di Azure da cui si vuole accedere al volume.  

        Per la rete virtuale specificata è necessario delegare una subnet ad Azure NetApp Files. Il servizio Azure NetApp Files è accessibile solo dalla stessa rete virtuale o da una rete virtuale presente nella stessa area del volume tramite il peering delle reti virtuali. È anche possibile accedere al volume dalla rete locale tramite ExpressRoute.   

    * **Subnet**  
        Specificare la subnet desiderata per il volume.  
        La subnet specificata deve essere delegata ad Azure NetApp Files. 
        
        Se non è stata delegata una subnet, fare clic su **Crea nuovo** nella pagina di creazione di un volume. Nella pagina di creazione della subnet, specificare le informazioni relative alla stessa e selezionare **Microsoft.NetApp/volumi** per delegarla ad Azure NetApp Files. Si noti che in ogni rete virtuale è possibile delegare una sola subnet ad Azure NetApp Files.   
 
        ![Nuovo volume](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Creare una subnet](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)


4.  Fare clic su **OK**. 
 
Un volume eredita sottoscrizione, gruppo di risorse e attributi di posizione dal relativo pool di capacità. Per monitorare lo stato di distribuzione del volume, è possibile usare la scheda Notifiche.

## <a name="next-steps"></a>Passaggi successivi  
* [Configurare i criteri di esportazione per un volume (facoltativo)](azure-netapp-files-configure-export-policy.md)
* [Informazioni sull'integrazione delle reti virtuali per i servizi di Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)

