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
ms.topic: conceptual
ms.date: 3/17/2019
ms.author: b-juche
ms.openlocfilehash: 3c59fb6abe9c26e6886706dae360ff40787e8faa
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2019
ms.locfileid: "59549185"
---
# <a name="create-a-volume-for-azure-netapp-files"></a>Creare un volume per Azure NetApp Files

Ogni pool di capacità può avere fino a 500 volumi. L'utilizzo della capacità di un volume concorre al calcolo della capacità di cui è stato effettuato il provisioning del pool. File di Azure NetApp supporta volumi NFS e a SMBv3. 

## <a name="before-you-begin"></a>Prima di iniziare 
È necessario avere già configurato un pool di capacità.   
[Configurare un pool di capacità](azure-netapp-files-set-up-capacity-pool.md)   
È necessario delegare una subnet ad Azure NetApp Files.  
[Delegare una subnet ad Azure NetApp Files](azure-netapp-files-delegate-subnet.md)

## <a name="create-an-nfs-volume"></a>Creare un volume NFS

1.  Scegliere il **volumi** pannello nel pannello del pool di capacità. 

    ![Passare ai volumi](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2.  Fare clic su **+ Aggiungi volume** per creare un volume.  
    La creazione che verrà visualizzata una finestra del Volume.

3.  Nella casella Crea una finestra del Volume, fare clic su **Create** e fornire informazioni per i campi seguenti:   
    * **Nome del volume**      
        Specificare il nome per il volume che si sta creando.   

        Il nome deve essere univoco all'interno di un gruppo di risorse. Deve essere composto da almeno tre caratteri.  È possibile usare qualsiasi carattere alfanumerico.

    * **Pool di capacità**  
        Specificare il pool di capacità in cui si desidera il volume da creare.

    * **Quota**  
        Specificare la quantità di spazio di archiviazione logico allocato al volume.  

        Il campo **Quota disponibile** mostra la quantità di spazio inutilizzato nel pool di capacità scelto che è possibile usare per la creazione di un nuovo volume. Le dimensioni del nuovo volume non devono superare la quota disponibile.  

    * **Rete virtuale**  
        Specificare la rete virtuale di Azure da cui si vuole accedere al volume.  

        Per la rete virtuale specificata è necessario delegare una subnet ad Azure NetApp Files. Il servizio Azure NetApp Files è accessibile solo dalla stessa rete virtuale o da una rete virtuale presente nella stessa area del volume tramite il peering delle reti virtuali. È anche possibile accedere il volume dalla rete locale tramite Express Route.   

    * **Subnet**  
        Specificare la subnet desiderata per il volume.  
        La subnet specificata deve essere delegata ad Azure NetApp Files. 
        
        Se non è stata delegata una subnet, fare clic su **Crea nuovo** nella pagina di creazione di un volume. Nella pagina di creazione della subnet, specificare le informazioni relative alla stessa e selezionare **Microsoft.NetApp/volumi** per delegarla ad Azure NetApp Files. In ogni rete virtuale, solo una subnet può essere delegata NetApp in file di Azure.   
 
        ![Crea un volume](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Creare una subnet](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. Fare clic su **Protocol**, quindi selezionare **NFS** come tipo di protocollo per il volume.   
    * Specificare il **percorso file** che verrà utilizzato per creare il percorso di esportazione per il nuovo volume. Il percorso di esportazione viene usato per montare il volume e accedervi.

        Il nome di percorso di file può contenere solo lettere, numeri e trattini ("-"). Il nome deve avere una lunghezza compresa tra 16 e 40 caratteri.  

    * Facoltativamente, [configurare i criteri di esportazione per il volume NFS](azure-netapp-files-configure-export-policy.md)

    ![Specificare il protocollo NFS](../media/azure-netapp-files/azure-netapp-files-protocol-nfs.png)

5. Fare clic su **Rivedi e crea** per esaminare i dettagli di volume.  Quindi fare clic su **Create** per creare il volume NFS.

    Il volume creato viene visualizzato nella pagina volumi. 
 
    Un volume eredita sottoscrizione, gruppo di risorse e attributi di posizione dal relativo pool di capacità. Per monitorare lo stato di distribuzione del volume, è possibile usare la scheda Notifiche.

## <a name="create-an-smb-volume"></a>Creare un volume di SMB

File di NetApp Azure supporta i volumi a SMBv3. È necessario creare connessioni di Active Directory prima di aggiungere un volume SMB. 

### <a name="create-an-active-directory-connection"></a>Creare una connessione di Active Directory

1. Dall'account di NetApp, fare clic su **connessioni Active Directory**, quindi fare clic su **Join**.  

    ![Connessioni di Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-connections.png)

2. Nella finestra di partecipare a Active Directory, fornire le informazioni seguenti:

    * **DNS primario**   
        Questo è l'indirizzo IP di controller di dominio per il preferito Active Directory Domain Services per l'utilizzo con file di Azure NetApp. 
    * **DNS secondario**  
        Questo è l'indirizzo IP di controller di dominio per il secondario Active Directory Domain Services per l'utilizzo con file di Azure NetApp. 
    * **Dominio**  
        Si tratta del nome di dominio di Active Directory Domain Services che si desidera aggiungere.
    * **Prefisso SMB server (account del computer)**  
        Si tratta del prefisso dei nomi per l'account del computer in Active Directory che i file di Azure NetApp utilizzerà per la creazione di nuovi account.

        Ad esempio, se lo standard di denominazione usato dall'organizzazione per i file server NAS-01, NAS-02..., NAS 045, quindi immettere "NA" per il prefisso. 

        Il servizio creerà gli account dei computer aggiuntivi in Active Directory in base alle esigenze.

    * **Percorso dell'unità organizzativa**  
        Questo è il percorso LDAP per l'unità organizzativa (OU) in cui verranno creati gli account di computer server SMB. Vale a dire, OU = secondo livello, OU = di primo livello. 
    * Le credenziali, inclusi i **nomeutente** e **password**

    ![Aggiungi Active Directory](../media/azure-netapp-files/azure-netapp-files-join-active-directory.png)

3. Fare clic su **Accedi**.  

    Viene visualizzata la connessione di Active Directory creata.

    ![Connessioni di Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-connections-created.png)

### <a name="add-an-smb-volume"></a>Aggiungere un volume di SMB

1. Scegliere il **volumi** pannello nel pannello del pool di capacità. 

    ![Passare ai volumi](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. Fare clic su **+ Aggiungi volume** per creare un volume.  
    La creazione che verrà visualizzata una finestra del Volume.

3. Nella casella Crea una finestra del Volume, fare clic su **Create** e fornire informazioni per i campi seguenti:   
    * **Nome del volume**      
        Specificare il nome per il volume che si sta creando.   

        Il nome deve essere univoco all'interno di un gruppo di risorse. Deve essere composto da almeno tre caratteri.  È possibile usare qualsiasi carattere alfanumerico.

    * **Percorso file**  
        Specificare il percorso del file che verrà usato per creare il percorso di esportazione per il nuovo volume. Il percorso di esportazione viene usato per montare il volume e accedervi.   
     
        Il nome di percorso di file può contenere solo lettere, numeri e trattini ("-"). Il nome deve avere una lunghezza compresa tra 16 e 40 caratteri.  

    * **Pool di capacità**  
        Specificare il pool di capacità in cui si desidera il volume da creare.

    * **Quota**  
        Specificare la quantità di spazio di archiviazione logico allocato al volume.  

        Il campo **Quota disponibile** mostra la quantità di spazio inutilizzato nel pool di capacità scelto che è possibile usare per la creazione di un nuovo volume. Le dimensioni del nuovo volume non devono superare la quota disponibile.  

    * **Rete virtuale**  
        Specificare la rete virtuale di Azure da cui si vuole accedere al volume.  

        Per la rete virtuale specificata è necessario delegare una subnet ad Azure NetApp Files. Il servizio Azure NetApp Files è accessibile solo dalla stessa rete virtuale o da una rete virtuale presente nella stessa area del volume tramite il peering delle reti virtuali. È anche possibile accedere il volume dalla rete locale tramite Express Route.   

    * **Subnet**  
        Specificare la subnet desiderata per il volume.  
        La subnet specificata deve essere delegata ad Azure NetApp Files. 
        
        Se non è stata delegata una subnet, fare clic su **Crea nuovo** nella pagina di creazione di un volume. Nella pagina di creazione della subnet, specificare le informazioni relative alla stessa e selezionare **Microsoft.NetApp/volumi** per delegarla ad Azure NetApp Files. In ogni rete virtuale, solo una subnet può essere delegata NetApp in file di Azure.   
 
        ![Crea un volume](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Creare una subnet](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. Fare clic su **protocollo** e completare le informazioni seguenti:  
    * Selezionare **SMB** come tipo di protocollo per il volume. 
    * Selezionare i **Active Directory** connessione nell'elenco a discesa.
    * Specificare il nome del volume condiviso nel **nome della condivisione**.

    ![Specificare il protocollo SMB](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. Fare clic su **Rivedi e crea** per esaminare i dettagli di volume.  Quindi fare clic su **Create** per creare il volume SMB.

    Il volume creato viene visualizzato nella pagina volumi. 
 
    Un volume eredita sottoscrizione, gruppo di risorse e attributi di posizione dal relativo pool di capacità. Per monitorare lo stato di distribuzione del volume, è possibile usare la scheda Notifiche.

## <a name="next-steps"></a>Passaggi successivi  

* [Montare o smontare un volume per le macchine virtuali Windows o Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Configurare i criteri di esportazione per un volume NFS](azure-netapp-files-configure-export-policy.md)
* [Informazioni sull'integrazione delle reti virtuali per i servizi di Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
