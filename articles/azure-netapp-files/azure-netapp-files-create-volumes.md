---
title: Creare un volume NFS per Azure NetApp Files | Microsoft Docs
description: Questo articolo illustra come creare un volume NFS in Azure NetApp Files. Informazioni sulle considerazioni, ad esempio la versione da usare e le procedure consigliate.
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
ms.date: 09/24/2020
ms.author: b-juche
ms.openlocfilehash: e88cf0c063b3ba42dadd72c19c23be8fcf919b86
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91930731"
---
# <a name="create-an-nfs-volume-for-azure-netapp-files"></a>Creare un volume NFS per Azure NetApp Files

Azure NetApp Files supporta la creazione di volumi tramite NFS (NFSv3 e NFSv 4.1), SMBv3 o il protocollo Dual (NFSv3 e SMB). L'utilizzo della capacità di un volume concorre al calcolo della capacità di cui è stato effettuato il provisioning del pool. Questo articolo illustra come creare un volume NFS. 

## <a name="before-you-begin"></a>Prima di iniziare 
* È necessario avere già configurato un pool di capacità.  
    Vedere [configurare un pool di capacità](azure-netapp-files-set-up-capacity-pool.md).   
* È necessario delegare una subnet ad Azure NetApp Files.  
    Vedere [delegare una subnet a Azure NetApp files](azure-netapp-files-delegate-subnet.md).

## <a name="considerations"></a>Considerazioni 

* Scelta della versione NFS da usare  
  NFSv3 è in grado di gestire un'ampia gamma di casi d'uso ed è comunemente distribuito nella maggior parte delle applicazioni aziendali. È necessario convalidare la versione (NFSv3 o NFSv 4.1) richiesta dall'applicazione e creare il volume utilizzando la versione appropriata. Ad esempio, se si usa [Apache ActiveMQ](https://activemq.apache.org/shared-file-system-master-slave), è consigliabile bloccare i file con NFSv 4.1 su NFSv3. 

* Sicurezza  
  Il supporto per BITS in modalità UNIX (lettura, scrittura ed esecuzione) è disponibile per NFSv3 e NFSv 4.1. Per il montaggio dei volumi NFS è necessario l'accesso a livello di radice nel client NFS.

* Utente/gruppo locale e supporto LDAP per NFSv 4.1  
  Attualmente, NFSv 4.1 supporta l'accesso alla radice solo ai volumi. Vedere [configurare il dominio predefinito NFSv 4.1 per Azure NetApp files](azure-netapp-files-configure-nfsv41-domain.md). 

## <a name="best-practice"></a>Procedura consigliata

* Assicurarsi di usare le istruzioni di montaggio appropriate per il volume.  Vedere [montare o smontare un volume per le macchine virtuali Windows o Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md).

* Il client NFS deve trovarsi nello stesso VNet o VNet con peering del volume Azure NetApp Files. La connessione dall'esterno di VNet è supportata. Tuttavia, verrà introdotta una latenza aggiuntiva e le prestazioni complessive diminuiranno.

* Verificare che il client NFS sia aggiornato ed esegua gli aggiornamenti più recenti per il sistema operativo.

## <a name="create-an-nfs-volume"></a>Creare un volume NFS

1.  Scegliere il pannello **Volumi** nel pannello Pool di capacità. Fare clic su **+ Aggiungi volume** per creare un volume. 

    ![Passare a Volumi](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png) 

2.  Nella finestra Crea un volume fare clic su **Crea**e fornire informazioni per i campi seguenti nella scheda nozioni di base:   
    * **Nome del volume**      
        Specificare il nome per il volume che si sta creando.   

        Un nome di volume deve essere univoco all'interno di ogni pool di capacità. Deve essere composto da almeno tre caratteri. È possibile usare qualsiasi carattere alfanumerico.   

        Non è possibile usare `default` o `bin` come nome del volume.

    * **Pool di capacità**  
        Specificare il pool di capacità in cui si vuole creare il volume.

    * **Quota**  
        Specificare la quantità di spazio di archiviazione logico allocato al volume.  

        Il campo **Quota disponibile** mostra la quantità di spazio inutilizzato nel pool di capacità scelto che è possibile usare per la creazione di un nuovo volume. Le dimensioni del nuovo volume non devono superare la quota disponibile.  

    * **Velocità effettiva (MiB/S)**   
        Se il volume viene creato in un pool di capacità QoS manuale, specificare la velocità effettiva desiderata per il volume.   

        Se il volume viene creato in un pool di capacità QoS automatica, il valore visualizzato in questo campo è (quota x velocità effettiva del livello di servizio).   

    * **Rete virtuale**  
        Specificare la rete virtuale di Azure da cui si vuole accedere al volume.  

        Per la rete virtuale specificata è necessario delegare una subnet ad Azure NetApp Files. Il servizio Azure NetApp Files è accessibile solo dalla stessa rete virtuale o da una rete virtuale presente nella stessa area del volume tramite il peering delle reti virtuali. È anche possibile accedere al volume dalla rete locale tramite ExpressRoute.   

    * **Subnet**  
        Specificare la subnet desiderata per il volume.  
        La subnet specificata deve essere delegata ad Azure NetApp Files. 
        
        Se non è stata delegata una subnet, fare clic su **Crea nuovo** nella pagina di creazione di un volume. Nella pagina di creazione della subnet, specificare le informazioni relative alla stessa e selezionare **Microsoft.NetApp/volumi** per delegarla ad Azure NetApp Files. In ogni VNET è possibile delegare una sola subnet a Azure NetApp Files.   
 
        ![Crea un volume](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Creare una subnet](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

    * Se si desidera applicare un criterio snapshot esistente al volume, fare clic su **Mostra sezione avanzata** per espanderlo, specificare se si desidera nascondere il percorso dello snapshot e selezionare un criterio di snapshot nel menu a discesa. 

        Per informazioni sulla creazione di un criterio snapshot, vedere [Manage snapshot Policies](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies).

        ![Mostra selezione avanzata](../media/azure-netapp-files/volume-create-advanced-selection.png)

3. Fare clic su **Protocollo** e quindi completare le azioni seguenti:  
    * Selezionare **NFS** come tipo di protocollo per il volume.   
    * Specificare il **percorso del file** che verrà usato per creare il percorso di esportazione del nuovo volume. Il percorso di esportazione viene usato per montare il volume e accedervi.

        Il nome di percorso di file può contenere solo lettere, numeri e trattini ("-"). Il nome deve avere una lunghezza compresa tra 16 e 40 caratteri. 

        Il percorso del file deve essere univoco all'interno di ogni sottoscrizione e di ogni area. 

    * Selezionare la versione di NFS (**NFSv3** o **NFSv4.1**) per il volume.  

    * Se si usa NFSv 4.1, indicare se si vuole abilitare la crittografia **Kerberos** per il volume.  

        Se si usa Kerberos con NFSv 4.1, sono necessarie configurazioni aggiuntive. Seguire le istruzioni in [configurare la crittografia Kerberos NFSv 4.1](configure-kerberos-encryption.md).

    * Facoltativamente, [configurare i criteri di esportazione per il volume NFS](azure-netapp-files-configure-export-policy.md).

    ![Specificare il protocollo NFS](../media/azure-netapp-files/azure-netapp-files-protocol-nfs.png)

4. Fare clic su **Rivedi e crea** per esaminare i dettagli del volume.  Fare quindi clic su **Crea** per creare il volume.

    Il volume creato viene visualizzato nella pagina Volumi. 
 
    Un volume eredita sottoscrizione, gruppo di risorse e attributi di posizione dal relativo pool di capacità. Per monitorare lo stato di distribuzione del volume, è possibile usare la scheda Notifiche.


## <a name="next-steps"></a>Passaggi successivi  

* [Configurare il dominio predefinito di NFS v4.1 per Azure NetApp Files](azure-netapp-files-configure-nfsv41-domain.md)
* [Configurare la crittografia Kerberos NFSv 4.1](configure-kerberos-encryption.md)
* [Montare o smontare un volume per macchine virtuali Windows o Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Configurare i criteri di esportazione per un volume NFS](azure-netapp-files-configure-export-policy.md)
* [Limiti delle risorse per Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Informazioni sull'integrazione delle reti virtuali per i servizi di Azure](../virtual-network/virtual-network-for-azure-services.md)