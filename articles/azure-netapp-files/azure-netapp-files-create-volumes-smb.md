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
ms.date: 04/03/2020
ms.author: b-juche
ms.openlocfilehash: c4e7566eeb28bc5709acd60ced9fcdffb7e8a725
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80668010"
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

* L'account amministratore usato deve avere la possibilità di creare account computer nel percorso unità organizzativa (OU) che si specificherà.  

* Le porte appropriate devono essere aperte nel server di Windows Active Directory (AD) applicabile.  
    Le porte necessarie sono le seguenti: 

    |     Servizio           |     Porta     |     Protocollo     |
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
    |    w32time            |    123       |    UDP           |

* La topologia del sito per il Active Directory Domain Services di destinazione deve rispettare le procedure consigliate, in particolare la VNet di Azure in cui viene distribuito Azure NetApp Files.  

    Lo spazio degli indirizzi per la rete virtuale in cui è distribuito Azure NetApp Files deve essere aggiunto a un sito di Active Directory nuovo o esistente (in cui un controller di dominio raggiungibile da Azure NetApp Files è). 

* I server DNS specificati devono essere raggiungibili dalla [subnet delegata](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet) di Azure NetApp files.  

    Vedere [linee guida per la pianificazione della rete Azure NetApp files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) per le topologie di rete supportate.

    I gruppi di sicurezza di rete (gruppi) e i firewall devono disporre di regole configurate in modo appropriato per consentire le richieste di traffico Active Directory e DNS. 

* Il Azure NetApp Files subnet delegata deve essere in grado di raggiungere tutti i Active Directory Domain Services (aggiunge) controller di dominio nel dominio, inclusi tutti i controller di dominio locali e remoti. In caso contrario, può verificarsi un'interruzione del servizio.  

    Se si dispone di controller di dominio non raggiungibili dal Azure NetApp Files subnet delegata, è possibile specificare un sito di Active Directory durante la creazione della connessione Active Directory.  Azure NetApp Files necessario comunicare solo con i controller di dominio nel sito in cui si trova lo spazio degli indirizzi della subnet delegata Azure NetApp Files.

    Vedere [progettazione della topologia del sito](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/designing-the-site-topology) sui siti e servizi di Active Directory. 
    
<!--
* Azure NetApp Files supports DES, Kerberos AES 128, and Kerberos AES 256 encryption types (from the least secure to the most secure). The user credentials used to join Active Directory must have the highest corresponding account option enabled that matches the capabilities enabled for your Active Directory.   

    For example, if your Active Directory has only the AES-128 capability, you must enable the AES-128 account option for the user credentials. If your Active Directory has the AES-256 capability, you must enable the AES-256 account option (which also supports AES-128). If your Active Directory does not have any Kerberos encryption capability, Azure NetApp Files uses DES by default.  

    You can enable the account options in the properties of the Active Directory Users and Computers MMC console:   

    ![Active Directory Users and Computers MMC](../media/azure-netapp-files/ad-users-computers-mmc.png)
-->

Vedere Azure NetApp Files [domande frequenti su SMB](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#smb-faqs) sulle informazioni aggiuntive su Active Directory. 

## <a name="decide-which-domain-services-to-use"></a>Decidere quali servizi del dominio usare 

Azure NetApp Files supporta sia [Active Directory Domain Services](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) (aggiunta) che Azure Active Directory Domain Services (AADDS) per le connessioni ad.  Prima di creare una connessione AD, è necessario decidere se utilizzare le operazioni di aggiunta o di AADDS.  

Per altre informazioni, vedere [confrontare Active Directory Domain Services autogestiti, Azure Active Directory e Azure Active Directory Domain Services gestiti](https://docs.microsoft.com/azure/active-directory-domain-services/compare-identity-solutions). 

### <a name="active-directory-domain-services"></a>Servizi di dominio di Active Directory

Per Azure NetApp Files è possibile usare i [siti e i servizi di Active Directory](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) preferiti. Questa opzione Abilita le letture e le Scritture per Active Directory Domain Services (aggiunge) controller [di dominio accessibili da Azure NetApp files](azure-netapp-files-network-topologies.md). Impedisce inoltre al servizio di comunicare con i controller di dominio che non si trovano nel sito Active Directory siti e servizi specificati. 

Per trovare il nome del sito quando si usa l'aggiunta, è possibile contattare il gruppo amministrativo nell'organizzazione responsabile della Active Directory Domain Services. Nell'esempio seguente viene illustrato il plug-in siti e servizi Active Directory in cui viene visualizzato il nome del sito: 

![Siti e servizi di Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-sites-and-services.png)

Quando si configura una connessione AD per Azure NetApp Files, specificare il nome del sito nell'ambito del campo **nome sito Active Directory** .

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services 

Per informazioni sulla configurazione di Azure Active Directory Domain Services (AADDS), vedere la [documentazione di Azure ad Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/).

Per Azure NetApp Files sono valide altre considerazioni relative a AADDS: 

* Assicurarsi che la VNet o la subnet in cui è distribuito AADDS si trovi nella stessa area di Azure della distribuzione del Azure NetApp Files.
* Se si usa un altro VNet nell'area in cui viene distribuito Azure NetApp Files, è necessario creare un peering tra i due reti virtuali.
* Azure NetApp Files supporta `user` i `resource forest` tipi e.
* Per tipo di sincronizzazione è possibile selezionare `All` o `Scoped`.   
    Se si seleziona `Scoped`, assicurarsi che sia selezionato il gruppo di Azure ad corretto per l'accesso alle condivisioni SMB.  Se non si è certi, è possibile usare il `All` tipo di sincronizzazione.
* È necessario usare lo SKU Enterprise o Premium. Lo SKU standard non è supportato.

Quando si crea una connessione Active Directory, tenere presenti le specifiche seguenti per AADDS:

* È possibile trovare informazioni per **DNS primario**, **DNS secondario**e **nome di dominio DNS di Active Directory** nel menu AADDS.  
Per i server DNS, verranno usati due indirizzi IP per la configurazione della connessione Active Directory. 
* Il **percorso dell'unità organizzativa** è `OU=AADDC Computers`.  
Questa impostazione è configurata nelle **connessioni Active Directory** sotto l' **account NetApp**:

  ![Percorso unità organizzativa](../media/azure-netapp-files/azure-netapp-files-org-unit-path.png)

* Le credenziali del **nome utente** possono essere qualsiasi utente membro del gruppo di Azure ad **Azure ad amministratori del controller**di dominio.


## <a name="create-an-active-directory-connection"></a>Creare una connessione Active Directory

1. Dall'account NetApp fare clic su **connessioni Active Directory**, quindi fare clic su **Aggiungi**.  

    ![Connessioni di Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-connections.png)

2. Nella finestra join Active Directory specificare le informazioni seguenti in base ai servizi del dominio che si desidera utilizzare:  

    Per informazioni specifiche dei servizi del dominio usati, vedere [decidere quali servizi del dominio usare](#decide-which-domain-services-to-use). 

    * **DNS primario**  
        Questo è il DNS necessario per le operazioni di aggiunta a un dominio Active Directory e di autenticazione SMB. 
    * **DNS secondario**   
        Si tratta del server DNS secondario per garantire i servizi dei nomi ridondanti. 
    * **Nome di dominio DNS AD**  
        Si tratta del nome di dominio del Active Directory Domain Services che si desidera aggiungere.
    * **Nome sito Active Directory**  
        Questo è il nome del sito a cui sarà limitata l'individuazione del controller di dominio.
    * **Prefisso server SMB (account computer)**  
        Questo è il prefisso di denominazione per l'account del computer in Active Directory che Azure NetApp Files utilizzerà per la creazione di nuovi account.

        Se, ad esempio, lo standard di denominazione usato dall'organizzazione per i file server è NAS-01, NAS-02..., NAS-045, immettere "NAS" per il prefisso. 

        Il servizio creerà account computer aggiuntivi in Active Directory in base alle esigenze.

    * **Percorso unità organizzativa**  
        Si tratta del percorso LDAP per l'unità organizzativa (OU) in cui verranno creati gli account del computer server SMB. Ovvero OU = Second Level, OU = First level. 

        Se si usa Azure NetApp Files con Azure Active Directory Domain Services, il percorso dell'unità organizzativa è `OU=AADDC Computers` quando si configura Active Directory per l'account NetApp.
        
    * Credenziali, inclusi **nome utente** e **password**

    ![Unisci Active Directory](../media/azure-netapp-files/azure-netapp-files-join-active-directory.png)

3. Fare clic su **Accedi**.  

    Viene visualizzata la connessione Active Directory creata.

    ![Connessioni di Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-connections-created.png)

> [!NOTE] 
> È possibile modificare i campi nome utente e password dopo avere salvato la connessione Active Directory. Non è possibile modificare altri valori dopo aver salvato la connessione. Se è necessario modificare altri valori, è necessario innanzitutto eliminare tutti i volumi SMB distribuiti, quindi eliminare e ricreare la connessione Active Directory.

## <a name="add-an-smb-volume"></a>Aggiungere un volume SMB

1. Fare clic sul pannello **volumi** nel pannello pool di capacità. 

    ![Passa a volumi](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. Fare clic su **+ Aggiungi volume** per creare un volume.  
    Viene visualizzata la finestra Crea un volume.

3. Nella finestra Crea un volume fare clic su **Crea** e fornire informazioni per i campi seguenti:   
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
        Specificare la rete virtuale di Azure (VNet) da cui si desidera accedere al volume.  

        Il VNet specificato deve avere una subnet delegata a Azure NetApp Files. È possibile accedere al servizio Azure NetApp Files solo dallo stesso VNet o da un VNet che si trova nella stessa area del volume tramite il peering VNet. È anche possibile accedere al volume dalla rete locale tramite Express route.   

    * **Subnet**  
        Specificare la subnet desiderata per il volume.  
        La subnet specificata deve essere delegata ad Azure NetApp Files. 
        
        Se non è stata delegata una subnet, è possibile fare clic su **Crea nuovo** nella pagina Crea un volume. Nella pagina di creazione della subnet, specificare le informazioni relative alla stessa e selezionare **Microsoft.NetApp/volumi** per delegarla ad Azure NetApp Files. In ogni VNet è possibile delegare una sola subnet a Azure NetApp Files.   
 
        ![Creare un volume](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Creare una subnet](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

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
* [Informazioni sull'integrazione della rete virtuale per i servizi di Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
* [Installare una nuova foresta Active Directory usando l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm)
