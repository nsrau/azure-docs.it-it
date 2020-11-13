---
title: Creazione di un volume a doppio protocollo (NFSv3 e SMB) per Azure NetApp Files | Microsoft Docs
description: Viene descritto come creare un volume che usa il protocollo duale di NFSv3 e SMB con supporto per il mapping utente LDAP.
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
ms.date: 10/12/2020
ms.author: b-juche
ms.openlocfilehash: 4fa2c724906c8a6bfb294541b6616ddc7ae22df6
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94591649"
---
# <a name="create-a-dual-protocol-nfsv3-and-smb-volume-for-azure-netapp-files"></a>Creazione di un volume a doppio protocollo (NFSv3 e SMB) per Azure NetApp Files

Azure NetApp Files supporta la creazione di volumi tramite NFS (NFSv3 e NFSv 4.1), SMBv3 o il protocollo duale. Questo articolo illustra come creare un volume che usa il protocollo duale di NFSv3 e SMB con supporto per il mapping utente LDAP.  


## <a name="before-you-begin"></a>Prima di iniziare 

* È necessario avere già creato un pool di capacità.  
    Vedere [configurare un pool di capacità](azure-netapp-files-set-up-capacity-pool.md).   
* È necessario delegare una subnet ad Azure NetApp Files.  
    Vedere [delegare una subnet a Azure NetApp files](azure-netapp-files-delegate-subnet.md).

## <a name="considerations"></a>Considerazioni

* Assicurarsi di soddisfare i [requisiti per le connessioni Active Directory](azure-netapp-files-create-volumes-smb.md#requirements-for-active-directory-connections). 
* Creare una zona di ricerca inversa nel server DNS, quindi aggiungere un record del puntatore (PTR) del computer host AD nella zona di ricerca inversa. In caso contrario, la creazione del volume con doppio protocollo avrà esito negativo.
* Verificare che il client NFS sia aggiornato ed esegua gli aggiornamenti più recenti per il sistema operativo.
* Verificare che il server LDAP di Active Directory (AD) sia attivo e in esecuzione nell'Active Directory. Questa operazione può essere eseguita installando e configurando il ruolo [Active Directory Lightweight Directory Services (ad LDS)](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/hh831593(v=ws.11)) nel computer ad.
* Assicurarsi che un'autorità di certificazione (CA) venga creata nell'annuncio utilizzando il ruolo [Servizi certificati Active Directory (ad CS)](/windows-server/networking/core-network-guide/cncg/server-certs/install-the-certification-authority) per generare ed esportare il certificato della CA radice autofirmato.   
* I volumi a doppio protocollo attualmente non supportano Azure Active Directory Domain Services (AADDS).  
* La versione NFS utilizzata da un volume a doppio protocollo è NFSv3. Di conseguenza, si applicano le considerazioni seguenti:
    * Il protocollo Dual non supporta gli attributi estesi degli ACL `set/get` di Windows dai client NFS.
    * I client NFS non possono modificare le autorizzazioni per lo stile di sicurezza NTFS e i client Windows non possono modificare le autorizzazioni per i volumi a doppio protocollo di tipo UNIX.   

    Nella tabella seguente vengono descritti gli stili di sicurezza e i relativi effetti:  
    
    | Stile di sicurezza    | Client che possono modificare le autorizzazioni   | Autorizzazioni che i client possono usare  | Stile di sicurezza effettivo risultante    | Client che possono accedere ai file     |
    |-  |-  |-  |-  |-  |
    | UNIX  | NFS   | Bit in modalità NFSv3   | UNIX  | NFS e Windows   |
    | NTFS  | Windows   | ACL NTFS     | NTFS  |NFS e Windows|

## <a name="create-a-dual-protocol-volume"></a>Creare un volume con doppio protocollo

1.  Scegliere il pannello **Volumi** nel pannello Pool di capacità. Fare clic su **+ Aggiungi volume** per creare un volume. 

    ![Passare a Volumi](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png) 

2.  Nella finestra Crea un volume fare clic su **Crea** e fornire informazioni per i campi seguenti nella scheda nozioni di base:   
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
    * Selezionare il **protocollo Dual (NFSv3 e SMB)** come tipo di protocollo per il volume.   

    * Selezionare la connessione **Active Directory** dall'elenco a discesa.  
    Il Active Directory che si utilizza deve disporre di un certificato CA radice del server. 

    * Specificare il **percorso del volume** per il volume.   
    Questo percorso del volume è il nome del volume condiviso. Il nome deve iniziare con un carattere alfabetico e deve essere univoco all'interno di ogni sottoscrizione e in ogni area.  

    * Specificare lo **stile di sicurezza** da utilizzare: NTFS (impostazione predefinita) o UNIX.

    * Facoltativamente, [configurare i criteri di esportazione per il volume](azure-netapp-files-configure-export-policy.md).

    ![Specifica doppio protocollo](../media/azure-netapp-files/create-volume-protocol-dual.png)

4. Fare clic su **Rivedi e crea** per esaminare i dettagli del volume. Fare quindi clic su **Crea** per creare il volume.

    Il volume creato viene visualizzato nella pagina Volumi. 
 
    Un volume eredita sottoscrizione, gruppo di risorse e attributi di posizione dal relativo pool di capacità. Per monitorare lo stato di distribuzione del volume, è possibile usare la scheda Notifiche.

## <a name="upload-active-directory-certificate-authority-public-root-certificate"></a>Carica certificato radice pubblico dell'autorità di certificazione Active Directory  

1.  Seguire [installare l'autorità di certificazione](/windows-server/networking/core-network-guide/cncg/server-certs/install-the-certification-authority) per installare e configurare l'aggiunta di un'autorità di certificazione. 

2.  Per utilizzare lo snap-in MMC e lo strumento Gestione certificati, [vedere visualizzare i certificati con lo snap-in MMC](/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in) .  
    Utilizzare lo snap-in Gestione certificati per individuare la radice o il certificato di emissione del dispositivo locale. È consigliabile eseguire i comandi dello snap-in Gestione certificati da una delle seguenti impostazioni:  
    * Un client basato su Windows che è stato aggiunto al dominio e in cui è installato il certificato radice 
    * Un altro computer nel dominio che contiene il certificato radice  

3. Esportare il certificato radice.  
    Verificare che il certificato venga esportato nel X. 509 con codifica base 64 (. Formato CER): 

    ![Esportazione guidata certificati](../media/azure-netapp-files/certificate-export-wizard.png)

4. Passare all'account NetApp del volume con doppio protocollo, fare clic su **Active Directory connessioni** e caricare il certificato CA radice usando la finestra **join Active Directory** :  

    ![Certificato CA radice server](../media/azure-netapp-files/server-root-ca-certificate.png)

    Verificare che il nome dell'autorità di certificazione possa essere risolto da DNS. Questo nome è il campo "rilasciato da" o "emittente" del certificato:  

    ![Informazioni relative al certificato](../media/azure-netapp-files/certificate-information.png)

## <a name="manage-ldap-posix-attributes"></a>Gestisci attributi POSIX LDAP

È possibile gestire gli attributi POSIX, ad esempio UID, Home Directory e altri valori, usando lo snap-in MMC utenti e computer Active Directory.  Nell'esempio seguente viene illustrato l'editor di attributi Active Directory:  

![Editor attributi Active Directory](../media/azure-netapp-files/active-directory-attribute-editor.png) 

È necessario impostare gli attributi seguenti per gli utenti LDAP e i gruppi LDAP: 
* Attributi obbligatori per gli utenti LDAP:   
    `uid`: Alice, `uidNumber` : 139, `gidNumber` : 555, `objectClass` : posixAccount
* Attributi obbligatori per i gruppi LDAP:   
    `objectClass`: "posixGroup", `gidNumber` : 555

## <a name="configure-the-nfs-client"></a>Configurare il client NFS 

Per configurare il client NFS, seguire le istruzioni riportate in [configurare un client NFS per Azure NetApp files](configure-nfs-clients.md) .  

## <a name="next-steps"></a>Passaggi successivi  

* [Configurare un client NFS per Azure NetApp Files](configure-nfs-clients.md)
* [Risolvere i problemi relativi ai volumi con doppio protocollo](troubleshoot-dual-protocol-volumes.md)