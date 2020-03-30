---
title: Creazione di un volume NFS per i file NetApp di Azure Documenti Microsoft
description: Viene descritto come creare un volume NFS per i file NetApp di Azure.Describes how to create an NFS volume for Azure NetApp Files.
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
ms.date: 12/01/2019
ms.author: b-juche
ms.openlocfilehash: 9e8817f802ca1d73ca0f6bfa2b32b1b14b37d7da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274086"
---
# <a name="create-an-nfs-volume-for-azure-netapp-files"></a>Creare un volume NFS per Azure NetApp Files

File NetApp di Azure supporta i volumi NFS (NFSv3 e NFSv4.1) e SMBv3. L'utilizzo della capacità di un volume concorre al calcolo della capacità di cui è stato effettuato il provisioning del pool. In questo articolo viene illustrato come creare un volume NFS. Se si vuole creare un volume SMB, vedere [Creare un volume SMB per i file NetApp](azure-netapp-files-create-volumes-smb.md)di Azure. 

## <a name="before-you-begin"></a>Prima di iniziare 
È necessario avere già configurato un pool di capacità.   
[Impostare un pool di capacità](azure-netapp-files-set-up-capacity-pool.md)   
È necessario delegare una subnet ad Azure NetApp Files.  
[Delegare una subnet ad Azure NetApp Files](azure-netapp-files-delegate-subnet.md)

## <a name="considerations"></a>Considerazioni 

* Scelta della versione di NFS da utilizzare  
  NFSv3 è in grado di gestire un'ampia gamma di casi d'uso ed è comunemente distribuito nella maggior parte delle applicazioni aziendali. È necessario convalidare la versione (NFSv3 o NFSv4.1) richiesta dall'applicazione e creare il volume utilizzando la versione appropriata. Ad esempio, se si utilizza [Apache ActiveMQ](https://activemq.apache.org/shared-file-system-master-slave), il blocco dei file con NFSv4.1 è consigliato su NFSv3. 

* Security  
  Il supporto per i bit in modalità UNIX (lettura, scrittura ed esecuzione) è disponibile per NFSv3 e NFSv4.1. L'accesso a livello di radice è necessario sul client NFS per montare i volumi NFS.

* Utente/gruppo locale e supporto LDAP per NFSv4.1  
  Attualmente, NFSv4.1 supporta l'accesso root solo ai volumi. Vedere [Configurare il dominio predefinito NFSv4.1 per i file NetApp](azure-netapp-files-configure-nfsv41-domain.md)di Azure. 

## <a name="best-practice"></a>Procedura consigliata

* È necessario assicurarsi di utilizzare le istruzioni di montaggio appropriate per il volume.  Vedere [Montare o smontare un volume per macchine virtuali Windows o Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md).

* Il client NFS deve trovarsi nella stessa rete virtuale o nella stessa rete virtuale con peered del volume File NetApp di Azure.The NFS client should be in the same VNet or peered VNet as the Azure NetApp Files volume. La connessione dall'esterno della rete virtuale è supportata; tuttavia, introdurrà una latenza aggiuntiva e ridurrà le prestazioni complessive.

* È necessario assicurarsi che il client NFS sia aggiornato ed esegua gli aggiornamenti più recenti per il sistema operativo.

## <a name="create-an-nfs-volume"></a>Creare un volume NFSCreate an NFS volume

1.  Fare clic sul pannello **Volumi** nel pannello Pool di capacità. 

    ![Passare a Volumi](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2.  Fare clic su **+ Aggiungi volume** per creare un volume.  
    Viene visualizzata la finestra Crea un volume.

3.  Nella finestra Crea un volume, fare clic su **Crea** e fornire le informazioni per i seguenti campi:   
    * **Nome volume**      
        Specificare il nome per il volume che si sta creando.   

        Un nome di volume deve essere univoco all'interno di ogni pool di capacità. Deve essere composto da almeno tre caratteri. È possibile utilizzare qualsiasi carattere alfanumerico.   

        Non è `default` possibile utilizzare come nome del volume.

    * **Pool di capacità**  
        Specificare il pool di capacità in cui si desidera creare il volume.

    * **Quota**  
        Specificare la quantità di spazio di archiviazione logico allocato al volume.  

        Il campo **Quota disponibile** mostra la quantità di spazio inutilizzato nel pool di capacità scelto che è possibile usare per la creazione di un nuovo volume. Le dimensioni del nuovo volume non devono superare la quota disponibile.  

    * **Rete virtuale**  
        Specificare la rete virtuale di Azure da cui si vuole accedere al volume.  

        Per la rete virtuale specificata è necessario delegare una subnet ad Azure NetApp Files. Il servizio Azure NetApp Files è accessibile solo dalla stessa rete virtuale o da una rete virtuale presente nella stessa area del volume tramite il peering delle reti virtuali. È inoltre possibile accedere al volume dalla rete locale tramite Express Route.   

    * **Subnet**  
        Specificare la subnet desiderata per il volume.  
        La subnet specificata deve essere delegata ad Azure NetApp Files. 
        
        Se non è stata delegata una subnet, fare clic su **Crea nuovo** nella pagina di creazione di un volume. Nella pagina di creazione della subnet, specificare le informazioni relative alla stessa e selezionare **Microsoft.NetApp/volumi** per delegarla ad Azure NetApp Files. In each Vnet, only one subnet can be delegated to Azure NetApp Files.   
 
        ![Creare un volume](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Creare una subnet](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. Fare clic su **Protocollo** e quindi completare le azioni seguenti:  
    * Selezionare **NFS** come tipo di protocollo per il volume.   
    * Specificare il percorso del **file** che verrà utilizzato per creare il percorso di esportazione per il nuovo volume. Il percorso di esportazione viene usato per montare il volume e accedervi.

        Il nome di percorso di file può contenere solo lettere, numeri e trattini ("-"). Il nome deve avere una lunghezza compresa tra 16 e 40 caratteri. 

        Il percorso del file deve essere univoco all'interno di ogni sottoscrizione e di ogni area. 

    * Selezionare la versione di NFS (**NFSv3** o **NFSv4.1**) per il volume.  
    * Facoltativamente, configurare i criteri di [esportazione per il volume NFS.](azure-netapp-files-configure-export-policy.md)

    ![Specificare il protocollo NFS](../media/azure-netapp-files/azure-netapp-files-protocol-nfs.png)

5. Fare clic su **Revisione e creazione** per esaminare i dettagli del volume.  Quindi fare clic su **Crea** per creare il volume NFS.

    Il volume creato viene visualizzato nella pagina Volumi. 
 
    Un volume eredita sottoscrizione, gruppo di risorse e attributi di posizione dal relativo pool di capacità. Per monitorare lo stato di distribuzione del volume, è possibile usare la scheda Notifiche.


## <a name="next-steps"></a>Passaggi successivi  

* [Configurare il dominio predefinito di NFS versione 4.1 per Azure NetApp Files](azure-netapp-files-configure-nfsv41-domain.md)
* [Montare o smontare un volume per macchine virtuali Windows o Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Configurare i criteri di esportazione per un volume NFS](azure-netapp-files-configure-export-policy.md)
* [Limiti delle risorse per Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Informazioni sull'integrazione della rete virtuale per i servizi di AzureLearn about virtual network integration for Azure services](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
