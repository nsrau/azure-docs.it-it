---
title: Creare un volume NFS per Azure NetApp Files | Microsoft Docs
description: Viene descritto come creare un volume NFS per Azure NetApp Files.
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
ms.date: 7/9/2019
ms.author: b-juche
ms.openlocfilehash: 45164acd89fc9634d6929bafb35e64a5dc9f2b86
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2019
ms.locfileid: "71178230"
---
# <a name="create-an-nfs-volume-for-azure-netapp-files"></a>Creare un volume NFS per Azure NetApp Files

Azure NetApp Files supporta i volumi NFS e SMBv3. L'utilizzo della capacità di un volume concorre al calcolo della capacità di cui è stato effettuato il provisioning del pool. Questo articolo illustra come creare un volume NFS. Se si vuole creare un volume SMB, vedere [creare un volume SMB per Azure NetApp files](azure-netapp-files-create-volumes-smb.md). 

## <a name="before-you-begin"></a>Prima di iniziare 
È necessario avere già configurato un pool di capacità.   
[Configurare un pool di capacità](azure-netapp-files-set-up-capacity-pool.md)   
È necessario delegare una subnet ad Azure NetApp Files.  
[Delegare una subnet ad Azure NetApp Files](azure-netapp-files-delegate-subnet.md)

## <a name="create-an-nfs-volume"></a>Creare un volume NFS

1.  Fare clic sul pannello **volumi** nel pannello pool di capacità. 

    ![Passa a volumi](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2.  Fare clic su **+ Aggiungi volume** per creare un volume.  
    Viene visualizzata la finestra Crea un volume.

3.  Nella finestra Crea un volume fare clic su **Crea** e fornire informazioni per i campi seguenti:   
    * **Nome volume**      
        Specificare il nome per il volume che si sta creando.   

        Un nome di volume deve essere univoco all'interno di ogni pool di capacità. Deve essere composto da almeno tre caratteri. È possibile utilizzare qualsiasi carattere alfanumerico.   

        Non è possibile `default` utilizzare come nome del volume.

    * **Pool di capacità**  
        Specificare il pool di capacità in cui si desidera creare il volume.

    * **Quota**  
        Specificare la quantità di spazio di archiviazione logico allocato al volume.  

        Il campo **Quota disponibile** mostra la quantità di spazio inutilizzato nel pool di capacità scelto che è possibile usare per la creazione di un nuovo volume. Le dimensioni del nuovo volume non devono superare la quota disponibile.  

    * **Rete virtuale**  
        Specificare la rete virtuale di Azure da cui si vuole accedere al volume.  

        Per la rete virtuale specificata è necessario delegare una subnet ad Azure NetApp Files. Il servizio Azure NetApp Files è accessibile solo dalla stessa rete virtuale o da una rete virtuale presente nella stessa area del volume tramite il peering delle reti virtuali. È anche possibile accedere al volume dalla rete locale tramite Express route.   

    * **Subnet**  
        Specificare la subnet desiderata per il volume.  
        La subnet specificata deve essere delegata ad Azure NetApp Files. 
        
        Se non è stata delegata una subnet, fare clic su **Crea nuovo** nella pagina di creazione di un volume. Nella pagina di creazione della subnet, specificare le informazioni relative alla stessa e selezionare **Microsoft.NetApp/volumi** per delegarla ad Azure NetApp Files. In ogni VNET è possibile delegare una sola subnet a Azure NetApp Files.   
 
        ![Crea un volume](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Crea subnet](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. Fare clic su **Protocollo** e quindi selezionare **NFS** come tipo di protocollo per il volume.   
    * Specificare il **percorso del file** che verrà usato per creare il percorso di esportazione del nuovo volume. Il percorso di esportazione viene usato per montare il volume e accedervi.

        Il nome di percorso di file può contenere solo lettere, numeri e trattini ("-"). Il nome deve avere una lunghezza compresa tra 16 e 40 caratteri. 

        Il percorso del file deve essere univoco all'interno di ogni sottoscrizione e di ogni area. 

    * Facoltativamente, [configurare i criteri di esportazione per il volume NFS](azure-netapp-files-configure-export-policy.md)

    ![Specificare il protocollo NFS](../media/azure-netapp-files/azure-netapp-files-protocol-nfs.png)

5. Fare clic su **Verifica + crea** per esaminare i dettagli del volume.  Fare quindi clic su **Crea** per creare il volume NFS.

    Il volume creato verrà visualizzato nella pagina volumi. 
 
    Un volume eredita sottoscrizione, gruppo di risorse e attributi di posizione dal relativo pool di capacità. Per monitorare lo stato di distribuzione del volume, è possibile usare la scheda Notifiche.


## <a name="next-steps"></a>Passaggi successivi  

* [Montare o smontare un volume per macchine virtuali Windows o Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Configurare i criteri di esportazione per un volume NFS](azure-netapp-files-configure-export-policy.md)
* [Limiti delle risorse per Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Informazioni sull'integrazione delle reti virtuali per i servizi di Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
