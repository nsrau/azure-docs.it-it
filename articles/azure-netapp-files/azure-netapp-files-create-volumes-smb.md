---
title: Creare un volume SMB per Azure NetApp Files | Microsoft Docs
description: Viene descritto come creare un volume SMB per Azure NetApp Files.
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
ms.openlocfilehash: 3cd60f390f0233e2923660fc39675b5a307d8d8f
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69515424"
---
# <a name="create-an-smb-volume-for-azure-netapp-files"></a>Creare un volume SMB per Azure NetApp Files

Azure NetApp Files supporta i volumi NFS e SMBv3. L'utilizzo della capacità di un volume concorre al calcolo della capacità di cui è stato effettuato il provisioning del pool. Questo articolo illustra come creare un volume SMBv3. Se si vuole creare un volume NFS, vedere [creare un volume NFS per Azure NetApp files](azure-netapp-files-create-volumes.md). 

## <a name="before-you-begin"></a>Prima di iniziare 
È necessario avere già configurato un pool di capacità.   
[Configurare un pool di capacità](azure-netapp-files-set-up-capacity-pool.md)   
È necessario delegare una subnet ad Azure NetApp Files.  
[Delegare una subnet ad Azure NetApp Files](azure-netapp-files-delegate-subnet.md)

## <a name="requirements-for-active-directory-connections"></a>Requisiti per le connessioni Active Directory

 È necessario creare connessioni di Active Directory prima di creare un volume SMB. I requisiti per le connessioni Active Directory sono i seguenti: 

* L'account amministratore usato deve essere in grado di creare account computer nel percorso unità organizzativa (OU) che verrà specificato.  

* Le porte appropriate devono essere aperte nel server di Windows Active Directory (AD) applicabile.  
    Le porte necessarie sono le seguenti: 

    |     Service           |     Port     |     Protocol     |
    |-----------------------|--------------|------------------|
    |    Servizi Web Active Directory    |    9389      |    TCP           |
    |    DNS                |    53        |    TCP           |
    |    DNS                |    53        |    UDP           |
    |    ICMPv4             |    N/D       |    Risposta echo    |
    |    Kerberos           |    464       |    TCP           |
    |    Kerberos           |    464       |    UDP           |
    |    Kerberos           |    88        |    TCP           |
    |    Kerberos           |    88        |    UDP           |
    |    LDAP               |    389       |    TCP           |
    |    LDAP               |    389       |    UDP           |
    |    LDAP               |    3268      |    TCP           |
    |    Nome NetBIOS       |    138       |    UDP           |
    |    SAM/LSA            |    445       |    TCP           |
    |    SAM/LSA            |    445       |    UDP           |
    |    LDAP sicuro        |    636       |    TCP           |
    |    LDAP sicuro        |    3269      |    TCP           |
    |    W32Time            |    123       |    UDP           |

* La topologia del sito per il Active Directory Domain Services di destinazione deve rispettare le procedure consigliate, in particolare la VNet di Azure in cui viene distribuito Azure NetApp Files.  

    Lo spazio degli indirizzi per la rete virtuale in cui è distribuito Azure NetApp Files deve essere aggiunto a un sito di Active Directory nuovo o esistente (in cui si trova un controller di dominio raggiungibile da Azure NetApp Files). 

* I server DNS specificati devono essere raggiungibili dalla [subnet](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet) delegata di Azure NetApp files.  

    Vedere [linee guida per la pianificazione della rete Azure NetApp files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) per le topologie di rete supportate.

    I gruppi di sicurezza di rete (gruppi) e i firewall devono disporre di regole configurate in modo appropriato per consentire le richieste di traffico Active Directory e DNS.

    Vedere [progettazione della topologia del sito](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/designing-the-site-topology) sui siti e servizi di Active Directory. 

## <a name="create-an-active-directory-connection"></a>Creare una connessione Active Directory

1. Dall'account NetApp fare clic su **connessioni Active Directory**, quindi fare clic su **Aggiungi**.  

    ![Connessioni di Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-connections.png)

2. Nella finestra join Active Directory specificare le informazioni seguenti:

    * **DNS primario**  
        Questo è il DNS necessario per le operazioni di aggiunta a un dominio Active Directory e di autenticazione SMB. 
    * **DNS secondario**   
        Si tratta del server DNS secondario per garantire i servizi dei nomi ridondanti. 
    * **Dominio**  
        Si tratta del nome di dominio del Active Directory Domain Services che si desidera aggiungere.
    * **Prefisso server SMB (account computer)**  
        Questo è il prefisso di denominazione per l'account del computer in Active Directory che Azure NetApp Files utilizzerà per la creazione di nuovi account.

        Se, ad esempio, lo standard di denominazione usato dall'organizzazione per i file server è NAS-01, NAS-02..., NAS-045, immettere "NAS" per il prefisso. 

        Il servizio creerà account computer aggiuntivi in Active Directory in base alle esigenze.

    * **Percorso unità organizzativa**  
        Si tratta del percorso LDAP per l'unità organizzativa (OU) in cui verranno creati gli account del computer server SMB. Ovvero OU = Second Level, OU = First level. 
    * Credenziali, inclusi **nome utente** e **password**

    ![Aggiungi Active Directory](../media/azure-netapp-files/azure-netapp-files-join-active-directory.png)

3. Fare clic su **Accedi**.  

    Viene visualizzata la connessione Active Directory creata.

    ![Connessioni di Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-connections-created.png)

## <a name="add-an-smb-volume"></a>Aggiungere un volume SMB

1. Fare clic sul pannello **volumi** nel pannello pool di capacità. 

    ![Passa a volumi](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. Fare clic su **+ Aggiungi volume** per creare un volume.  
    Viene visualizzata la finestra Crea un volume.

3. Nella finestra Crea un volume fare clic su **Crea** e fornire informazioni per i campi seguenti:   
    * **Nome volume**      
        Specificare il nome per il volume che si sta creando.   

        Un nome di volume deve essere univoco all'interno di ogni pool di capacità. Deve essere composto da almeno tre caratteri. È possibile utilizzare qualsiasi carattere alfanumerico.

    * **Pool di capacità**  
        Specificare il pool di capacità in cui si desidera creare il volume.

    * **Quota**  
        Specificare la quantità di spazio di archiviazione logico allocato al volume.  

        Il campo **Quota disponibile** mostra la quantità di spazio inutilizzato nel pool di capacità scelto che è possibile usare per la creazione di un nuovo volume. Le dimensioni del nuovo volume non devono superare la quota disponibile.  

    * **Rete virtuale**  
        Specificare la rete virtuale di Azure (VNet) da cui si desidera accedere al volume.  

        Il VNet specificato deve avere una subnet delegata a Azure NetApp Files. È possibile accedere al servizio Azure NetApp Files solo dallo stesso VNet o da un VNet che si trova nella stessa area del volume tramite il peering VNet. È anche possibile accedere al volume dalla rete locale tramite Express route.   

    * **Subnet**  
        Specificare la subnet desiderata per il volume.  
        La subnet specificata deve essere delegata ad Azure NetApp Files. 
        
        Se non è stata delegata una subnet, fare clic su **Crea nuovo** nella pagina di creazione di un volume. Nella pagina di creazione della subnet, specificare le informazioni relative alla stessa e selezionare **Microsoft.NetApp/volumi** per delegarla ad Azure NetApp Files. In ogni VNet è possibile delegare una sola subnet a Azure NetApp Files.   
 
        ![Crea un volume](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Crea subnet](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. Fare clic su **protocollo** e completare le seguenti informazioni:  
    * Selezionare **SMB** come tipo di protocollo per il volume. 
    * Selezionare la connessione **Active Directory** dall'elenco a discesa.
    * Specificare il nome del volume condiviso in **nome condivisione**.

    ![Specificare il protocollo SMB](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. Fare clic su **Verifica + crea** per esaminare i dettagli del volume.  Fare quindi clic su **Crea** per creare il volume SMB.

    Il volume creato verrà visualizzato nella pagina volumi. 
 
    Un volume eredita sottoscrizione, gruppo di risorse e attributi di posizione dal relativo pool di capacità. Per monitorare lo stato di distribuzione del volume, è possibile usare la scheda Notifiche.

## <a name="next-steps"></a>Passaggi successivi  

* [Montare o smontare un volume per macchine virtuali Windows o Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Limiti delle risorse per Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Domande frequenti su SMB](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#smb-faqs)
* [Informazioni sull'integrazione delle reti virtuali per i servizi di Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
* [Installare una nuova foresta Active Directory usando l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm)
