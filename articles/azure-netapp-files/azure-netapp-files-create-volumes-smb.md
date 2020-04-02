---
title: Creazione di un volume SMB per i file NetApp di Azure Documenti Microsoft
description: Viene descritto come creare un volume SMB per i file NetApp di Azure.Describes how to create an SMB volume for Azure NetApp Files.
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
ms.date: 03/31/2020
ms.author: b-juche
ms.openlocfilehash: 9ad9e13667791c38a8bf8be01919bcdbd0032102
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80519602"
---
# <a name="create-an-smb-volume-for-azure-netapp-files"></a>Creare un volume SMB per Azure NetApp Files

File NetApp di Azure supporta i volumi NFS e SMBv3. L'utilizzo della capacità di un volume concorre al calcolo della capacità di cui è stato effettuato il provisioning del pool. In questo articolo viene illustrato come creare un volume SMBv3. Se si vuole creare un volume NFS, vedere [Creare un volume NFS per i file NetApp](azure-netapp-files-create-volumes.md)di Azure. 

## <a name="before-you-begin"></a>Prima di iniziare 
È necessario avere già configurato un pool di capacità.   
[Impostare un pool di capacità](azure-netapp-files-set-up-capacity-pool.md)   
È necessario delegare una subnet ad Azure NetApp Files.  
[Delegare una subnet ad Azure NetApp Files](azure-netapp-files-delegate-subnet.md)

## <a name="requirements-for-active-directory-connections"></a>Requisiti per le connessioni active Directory

 È necessario creare connessioni active Directory prima di creare un volume SMB. I requisiti per le connessioni di Active Directory sono i seguenti: 

* L'account amministratore utilizzato deve disporre della possibilità di creare account computer nel percorso dell'unità organizzativa da specificare.  

* Le porte corrette devono essere aperte sul server Windows Active Directory (AD) applicabile.  
    Le porte richieste sono le seguenti: 

    |     Service           |     Porta     |     Protocollo     |
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

* La topologia del sito per Servizi di dominio Active Directory di destinazione deve rispettare le procedure consigliate, in particolare la rete virtuale di Azure in cui vengono distribuiti i file netApp di Azure.The site topology for the targeted Active Directory Domain Services must adhere to best practices, in particular the Azure VNet where Azure NetApp Files is deployed.  

    Lo spazio degli indirizzi per la rete virtuale in cui viene distribuito File NetApp di Azure deve essere aggiunto a un sito di Active Directory nuovo o esistente (dove è un controller di dominio raggiungibile da File di Rete di Azure). 

* I server DNS specificati devono essere raggiungibili dalla subnet delegata dei file NetApp di Azure.The specified DNS servers must be reachable from the [delegated subnet](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet) of Azure NetApp Files.  

    Vedere Linee guida per la pianificazione della rete di file di Rete di [Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) per le topologie di rete supportate.

    I gruppi di sicurezza di rete e i firewall devono disporre di regole configurate in modo appropriato per consentire le richieste di traffico Active Directory e DNS. 

* La subnet delegata file Di Azure NetApp deve essere in grado di raggiungere tutti i controller di dominio di servizi di dominio Active Directory (ADDS) nel dominio, inclusi tutti i controller di dominio locali e remoti. In caso contrario, può verificarsi un'interruzione del servizio.  

    Se si dispone di controller di dominio che non sono raggiungibili dalla subnet delegata File NetApp di Azure, è possibile specificare un sito di Active Directory durante la creazione della connessione di Active Directory.  File Di Rete di Azure deve comunicare solo con i controller di dominio nel sito in cui si trova lo spazio di indirizzi della subnet delegata File netApp di Azure.Azure NetApp Files needs to communicate only with domain controllers in the site where the Azure NetApp Files delegated subnet address space.

    Vedere [Progettazione della topologia del sito](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/designing-the-site-topology) relativa a siti e servizi di Active Directory. 
    
* File NetApp di Azure supporta i tipi di crittografia DES, Kerberos AES 128 e Kerberos AES 256 (dal meno sicuro al più sicuro). Le credenziali utente utilizzate per l'aggiunta ad Active Directory devono avere l'opzione di account corrispondente più alto abilitata che corrisponde alle funzionalità abilitate per Active Directory.   

    Ad esempio, se Active Directory dispone solo della funzionalità AES-128, è necessario abilitare l'opzione account AES-128 per le credenziali utente. Se Active Directory dispone della funzionalità AES-256, è necessario attivare l'opzione account AES-256 (che supporta anche AES-128). Se Active Directory non dispone di alcuna funzionalità di crittografia Kerberos, i file di Azure NetApp utilizzaNO DES per impostazione predefinita.  

    È possibile attivare le opzioni dell'account nelle proprietà della console MMC Utenti e computer di Active Directory:   

    ![MMC Utenti e computer di Active Directory](../media/azure-netapp-files/ad-users-computers-mmc.png)

Vedere Domande frequenti su Azure NetApp Files SMB su informazioni aggiuntive di Active Directory.See Azure NetApp Files [SMB FAQs](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#smb-faqs) about additional AD information. 

## <a name="decide-which-domain-services-to-use"></a>Decidere quali servizi di dominio utilizzare 

File NetApp di Azure supporta sia Servizi di dominio Active Directory (ADDS) che Servizi di dominio Azure Active Directory (AADDS) per le connessioni di Active Directory.Azure NetApp Files supports both [Active Directory Domain Services](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) (ADDS) and Azure Active Directory Domain Services (AADDS) for AD connections.  Prima di creare una connessione AD, è necessario decidere se utilizzare ADDS o AADDS.  

Per altre informazioni, vedere Confronto di servizi di [dominio Active Directory autogestito, Azure Active Directory e Servizi](https://docs.microsoft.com/azure/active-directory-domain-services/compare-identity-solutions)di dominio Azure Active Directory gestiti. 

### <a name="active-directory-domain-services"></a>Active Directory Domain Services

È possibile usare l'ambito siti e servizi di Active Directory preferito per i file NetApp di Azure.You can use your preferred [Active Directory Sites and Services](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) scope for Azure NetApp Files. Questa opzione abilita le operazioni di lettura e scrittura nei controller di dominio di Servizi di dominio Active Directory accessibili dai file netApp di [Azure.](azure-netapp-files-network-topologies.md) Impedisce inoltre al servizio di comunicare con i controller di dominio che non si trovano nel sito Siti e servizi di Active Directory specificato. 

Per trovare il nome del sito quando si utilizza ADDS, è possibile contattare il gruppo amministrativo dell'organizzazione responsabile di Servizi di dominio Active Directory. L'esempio seguente mostra il plug-in Siti e servizi di Active Directory in cui viene visualizzato il nome del sito: 

![Siti e servizi di Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-sites-and-services.png)

Quando si configura una connessione AD per i file NetApp di Azure, si specifica il nome del sito nell'ambito per il campo **Nome sito Active Directory.**

### <a name="azure-active-directory-domain-services"></a>Servizi di dominio Azure Active Directory 

Per istruzioni e la configurazione di Servizi di dominio Azure Active Directory (AADDS), vedere la documentazione di Servizi di [dominio Azure AD.](https://docs.microsoft.com/azure/active-directory-domain-services/)

Ulteriori considerazioni Su AADDS si applicano ai file NetApp di Azure:Additional AADDS considerations apply for Azure NetApp Files: 

* Verificare che la rete virtuale o la subnet in cui è distribuito AADDS si trova nella stessa area di Azure della distribuzione file di Azure NetApp.Ensure the VNet or subnet where AADDS is deployed is in the same Azure region as the Azure NetApp Files deployment.
* Se si usa un'altra rete virtuale nell'area in cui vengono distribuiti i file NetApp di Azure, è necessario creare un peering tra le due reti virtuali.
* I tipi e `user` i `resource forest` file NetApp di Azure.Azure NetApp Files supports and types.
* Per il tipo di `All` `Scoped`sincronizzazione, è possibile selezionare o .   
    Se si `Scoped`seleziona , verificare che sia selezionato il gruppo di Azure AD corretto per l'accesso alle condivisioni SMB.  Se non si è certi, `All` è possibile utilizzare il tipo di sincronizzazione.
* È necessario utilizzare lo SKU Enterprise o Premium. Lo SKU Standard non è supportato.

Quando si crea una connessione Active Directory, tenere presenti le seguenti specifiche per AADDS:

* È possibile trovare informazioni su **DNS primario,** **DNS secondario**e **nome di dominio DNS di Active Directory** nel menu AADDS.  
Per i server DNS, per la configurazione della connessione di Active Directory verranno utilizzati due indirizzi IP. 
* Il **percorso dell'unità organizzativa** è `OU=AADDC Computers`.  
Questa impostazione è configurata in **Connessioni di Active Directory** in Account **NetApp**:

  ![Percorso unità organizzativa](../media/azure-netapp-files/azure-netapp-files-org-unit-path.png)

* **Le** credenziali del nome utente possono essere qualsiasi utente membro del gruppo di Azure AD **Amministratori controller di dominio Azure AD**.


## <a name="create-an-active-directory-connection"></a>Creare una connessione ad Active Directory

1. Dall'account NetApp, fare clic su **Connessioni di Active Directory**, quindi su **Partecipa**.  

    ![Connessioni di Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-connections.png)

2. Nella finestra Aggiungi ad Active Directory, fornire le seguenti informazioni, in base ai servizi di dominio che si desidera utilizzare:  

    Per informazioni specifiche sui Servizi di dominio in uso, vedere [Decidere quali servizi di dominio utilizzare.](#decide-which-domain-services-to-use) 

    * **DNS primario**  
        Si tratta del DNS necessario per l'aggiunta al dominio di Active Directory e le operazioni di autenticazione SMB. 
    * **DNS secondario**   
        Si tratta del server DNS secondario per garantire servizi con nomi ridondanti. 
    * **Nome dominio DNS di Active Directory**  
        Si tratta del nome di dominio di Servizi di dominio Active Directory a cui si desidera partecipare.
    * **Nome sito Ad**  
        Questo è il nome del sito a cui sarà limitata l'individuazione del controller di dominio.
    * **Prefisso server SMB (account computer)**  
        Questo è il prefisso di denominazione per l'account del computer in Active Directory che i file NetApp di Azure utilizzeranno per la creazione di nuovi account.

        Ad esempio, se lo standard di denominazione utilizzato dall'organizzazione per i file server è NAS-01, NAS-02..., NAS-045, immettere "NAS" come prefisso. 

        Il servizio creerà account computer aggiuntivi in Active Directory in base alle esigenze.

    * **Percorso unità organizzativa**  
        Si tratta del percorso LDAP dell'unità organizzativa (OU) in cui verranno creati gli account computer server SMB. Ovvero, unità Organizzativa, secondo livello, unità Organizzativa, primo livello. 

        Se si usano file NetApp di Azure con Servizi di `OU=AADDC Computers` dominio Azure Active Directory, il percorso dell'unità organizzativa si verifica quando si configura Active Directory per l'account NetApp.
        
    * Credenziali, inclusi **nome utente** e **password**

    ![Entra in Active Directory](../media/azure-netapp-files/azure-netapp-files-join-active-directory.png)

3. Fare clic su **Accedi**.  

    Viene visualizzata la connessione ad Active Directory creata.

    ![Connessioni di Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-connections-created.png)

> [!NOTE] 
> È possibile modificare i campi del nome utente e della password dopo aver salvato la connessione ad Active Directory. Nessun altro valore può essere modificato dopo aver salvato la connessione. Se è necessario modificare altri valori, è innanzitutto necessario eliminare tutti i volumi SMB distribuiti, quindi eliminare e ricreare la connessione ad Active Directory.

## <a name="add-an-smb-volume"></a>Aggiungere un volume SMB

1. Fare clic sul pannello **Volumi** nel pannello Pool di capacità. 

    ![Passare a Volumi](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. Fare clic su **+ Aggiungi volume** per creare un volume.  
    Viene visualizzata la finestra Crea un volume.

3. Nella finestra Crea un volume, fare clic su **Crea** e fornire le informazioni per i seguenti campi:   
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

        La rete virtuale specificata deve avere una subnet delegata aFile di Azure NetApp.The VNet you specify must have a subnet delegated to Azure NetApp Files. È possibile accedere al servizio File NetApp di Azure solo dalla stessa rete virtuale o da una rete virtuale che si trova nella stessa area del volume tramite il peering della rete virtuale. È inoltre possibile accedere al volume dalla rete locale tramite Express Route.   

    * **Subnet**  
        Specificare la subnet desiderata per il volume.  
        La subnet specificata deve essere delegata ad Azure NetApp Files. 
        
        Se non è stata delegata una subnet, è possibile fare clic su **Crea nuova** nella pagina Crea un volume. Nella pagina di creazione della subnet, specificare le informazioni relative alla stessa e selezionare **Microsoft.NetApp/volumi** per delegarla ad Azure NetApp Files. In each VNet, only one subnet can be delegated to Azure NetApp Files.   
 
        ![Creare un volume](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Creare una subnet](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. Fare clic su **Protocollo** e completare le seguenti informazioni:  
    * Selezionare **SMB** come tipo di protocollo per il volume. 
    * Selezionare la connessione **ad Active Directory** dall'elenco a discesa.
    * Specificare il nome del volume condiviso in **Nome condivisione**.

    ![Specificare il protocollo SMB](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. Fare clic su **Revisione e creazione** per esaminare i dettagli del volume.  Quindi fare clic su **Crea** per creare il volume SMB.

    Il volume creato viene visualizzato nella pagina Volumi. 
 
    Un volume eredita sottoscrizione, gruppo di risorse e attributi di posizione dal relativo pool di capacità. Per monitorare lo stato di distribuzione del volume, è possibile usare la scheda Notifiche.

## <a name="next-steps"></a>Passaggi successivi  

* [Montare o smontare un volume per macchine virtuali Windows o Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Limiti delle risorse per Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Domande frequenti su SMB](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#smb-faqs)
* [Informazioni sull'integrazione della rete virtuale per i servizi di AzureLearn about virtual network integration for Azure services](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
* [Installare una nuova foresta di Active Directory usando l'interfaccia della riga di comando di AzureInstall a new Active Directory forest using Azure CLI](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm)
