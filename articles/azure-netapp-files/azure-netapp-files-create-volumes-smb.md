---
title: Creare un volume SMB per Azure NetApp Files | Microsoft Docs
description: Questo articolo illustra come creare un volume SMBv3 in Azure NetApp Files. Informazioni sui requisiti per le connessioni Active Directory e i servizi del dominio.
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
ms.openlocfilehash: 9740506da2c03996db756175551867ed43575a7c
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94488180"
---
# <a name="create-an-smb-volume-for-azure-netapp-files"></a>Creare un volume SMB per Azure NetApp Files

Azure NetApp Files supporta la creazione di volumi tramite NFS (NFSv3 e NFSv 4.1), SMBv3 o il protocollo Dual (NFSv3 e SMB). L'utilizzo della capacità di un volume concorre al calcolo della capacità di cui è stato effettuato il provisioning del pool. Questo articolo illustra come creare un volume SMBv3.

## <a name="before-you-begin"></a>Prima di iniziare 
È necessario avere già configurato un pool di capacità.   
[Configurare un pool di capacità](azure-netapp-files-set-up-capacity-pool.md)   
È necessario delegare una subnet ad Azure NetApp Files.  
[Delegare una subnet ad Azure NetApp Files](azure-netapp-files-delegate-subnet.md)

## <a name="requirements-for-active-directory-connections"></a>Requisiti per le connessioni Active Directory

 È necessario creare le connessioni Active Directory prima di creare un volume SMB. I requisiti per le connessioni Active Directory sono i seguenti: 

* L'account amministratore usato deve avere la possibilità di creare account computer nel percorso dell'unità organizzativa (OU) che verrà specificato.  

* Le porte appropriate devono essere aperte nel server Windows Active Directory (AD) applicabile.  
    Le porte richieste sono le seguenti: 

    |     Service           |     Porta     |     Protocollo     |
    |-----------------------|--------------|------------------|
    |    Servizi Web AD    |    9389      |    TCP           |
    |    DNS                |    53        |    TCP           |
    |    DNS                |    53        |    UDP           |
    |    ICMPv4             |    N/D       |    Echo Reply    |
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

* La topologia del sito per Active Directory Domain Services di destinazione deve rispettare le linee guida, in particolare la rete virtuale di Azure in cui viene distribuito Azure NetApp Files.  

    Lo spazio indirizzi per la rete virtuale in cui viene distribuito Azure NetApp Files deve essere aggiunto a un sito Active Directory nuovo o esistente (in cui risiede un controller di dominio raggiungibile da Azure NetApp Files). 

* I server DNS specificati devono essere raggiungibili dalla [subnet delegata](./azure-netapp-files-delegate-subnet.md) di Azure NetApp Files.  

    Per le topologie di rete supportate, vedere [Linee guida per la pianificazione della rete per Azure NetApp Files](./azure-netapp-files-network-topologies.md).

    I gruppi di sicurezza di rete (NSG) e i firewall devono disporre di regole configurate in modo appropriato per consentire le richieste di traffico Active Directory e DNS. 

* La subnet delegata di Azure NetApp Files deve essere in grado di raggiungere tutti i controller di dominio di Active Directory Domain Services (ADDS) nel dominio, inclusi tutti i controller di dominio locali e remoti. In caso contrario, può verificarsi un'interruzione del servizio.  

    Se si hanno controller di dominio non raggiungibili dalla subnet delegata di Azure NetApp Files, è possibile specificare un sito Active Directory durante la creazione della connessione Active Directory.  Azure NetApp Files deve comunicare solo con i controller di dominio nel sito in cui si trova lo spazio indirizzi della subnet delegata di Azure NetApp Files.

    Vedere [Progettazione della topologia del sito](/windows-server/identity/ad-ds/plan/designing-the-site-topology) per siti e servizi di Active Directory. 
    
* È possibile abilitare la crittografia AES per un volume SMB selezionando la casella **crittografia AES** nella finestra [join Active Directory](#create-an-active-directory-connection) . Azure NetApp Files supporta i tipi di crittografia DES, Kerberos AES 128 e Kerberos AES 256 (dal meno sicuro al più sicuro). Se si Abilita la crittografia AES, le credenziali utente usate per unire Active Directory devono avere l'opzione account corrispondente più alta abilitata che corrisponde alle funzionalità abilitate per il Active Directory.    

    Se, ad esempio, il Active Directory dispone solo della funzionalità AES-128, è necessario abilitare l'opzione relativa all'account AES-128 per le credenziali utente. Se il Active Directory ha la funzionalità AES-256, è necessario abilitare l'opzione relativa all'account AES-256, che supporta anche AES-128. Se il Active Directory non dispone di alcuna funzionalità di crittografia Kerberos, Azure NetApp Files USA DES per impostazione predefinita.  

    È possibile abilitare le opzioni dell'account nelle proprietà del Active Directory Microsoft Management Console (MMC) utenti e computer:   

    ![MMC utenti e computer Active Directory](../media/azure-netapp-files/ad-users-computers-mmc.png)

* Azure NetApp Files supporta la [firma LDAP](/troubleshoot/windows-server/identity/enable-ldap-signing-in-windows-server), che consente la trasmissione sicura del traffico LDAP tra il servizio Azure NetApp files e i [controller di dominio Active Directory](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)di destinazione. Se si seguono le linee guida di Microsoft Advisory [ADV190023](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190023) per la firma LDAP, è necessario abilitare la funzionalità di firma ldap in Azure NetApp files selezionando la casella **firma LDAP** nella finestra [join Active Directory](#create-an-active-directory-connection) . 

    La configurazione dell' [associazione di canale LDAP](https://support.microsoft.com/help/4034879/how-to-add-the-ldapenforcechannelbinding-registry-entry) non ha alcun effetto sul servizio Azure NetApp files. 

Vedere [Domande frequenti su SMB](./azure-netapp-files-faqs.md#smb-faqs) di Azure NetApp Files per informazioni aggiuntive su Active Directory. 

## <a name="decide-which-domain-services-to-use"></a>Decidere quali servizi di dominio usare 

Azure NetApp Files supporta sia [Active Directory Domain Services](/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) (ADDS) che Azure Active Directory Domain Services (AADDS) per le connessioni AD.  Prima di creare una connessione AD, è necessario decidere se usare ADDS o AADDS.  

Per altre informazioni, vedere [Confrontare soluzioni Active Directory Domain Services autogestite, Azure Active Directory e Azure Active Directory Domain Services gestite](../active-directory-domain-services/compare-identity-solutions.md). 

### <a name="active-directory-domain-services"></a>Active Directory Domain Services

È possibile usare l'ambito preferito di [Siti e servizi di Active Directory](/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) per Azure NetApp Files. Questa opzione consente letture e scritture nei controller di dominio di Active Directory Domain Services (ADDS) [accessibili da Azure NetApp Files](azure-netapp-files-network-topologies.md). Impedisce inoltre al servizio di comunicare con i controller di dominio che non si trovano nel sito specificato in Siti e servizi di Active Directory. 

Per trovare il nome del sito quando si usa ADDS, è possibile contattare il gruppo amministrativo dell'organizzazione responsabile di Active Directory Domain Services. Nell'esempio seguente viene illustrato il plug-in Siti e servizi di Active Directory in cui viene visualizzato il nome del sito: 

![Siti e servizi di Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-sites-services.png)

Quando si configura una connessione AD per Azure NetApp Files, è possibile specificare il nome del sito nell'ambito del campo **Nome sito di AD**.

### <a name="azure-active-directory-domain-services"></a>Servizi di dominio Azure Active Directory 

Per la configurazione di Azure Active Directory Domain Services (AADDS) e altre informazioni, vedere [Documentazione di Azure AD Domain Services](../active-directory-domain-services/index.yml).

Per Azure NetApp Files sono valide altre considerazioni relative a AADDS: 

* Assicurarsi che la rete virtuale o la subnet in cui viene distribuito AADDS si trovi nella stessa area di Azure della distribuzione di Azure NetApp Files.
* Se si usa un'altra rete virtuale nell'area in cui viene distribuito Azure NetApp Files, è necessario creare un peering tra le due reti virtuali.
* Azure NetApp Files supporta i tipi `user` e `resource forest`.
* Per tipo di sincronizzazione è possibile selezionare `All` o `Scoped`.   
    Se si seleziona `Scoped`, assicurarsi che sia selezionato il gruppo di Azure AD corretto per l'accesso alle condivisioni SMB.  Se non si è sicuri, è possibile usare il tipo di sincronizzazione `All`.
* È necessario usare lo SKU Enterprise o Premium. Lo SKU Standard non è supportato.

Quando si crea una connessione Active Directory, tenere presenti le specifiche seguenti per AADDS:

* È possibile trovare informazioni per **DNS primario** , **DNS secondario** e **Nome di dominio DNS di Active Directory** nel menu AADDS.  
Per i server DNS, verranno usati due indirizzi IP per la configurazione della connessione Active Directory. 
* Il **percorso unità organizzativa** è `OU=AADDC Computers`.  
Questa impostazione viene configurata in **Connessioni Active Directory** in **Account NetApp** :

  ![Percorso unità organizzativa](../media/azure-netapp-files/azure-netapp-files-org-unit-path.png)

* Le credenziali del **nome utente** possono essere qualsiasi utente membro del gruppo di **amministratori dei controller di dominio di Azure AD**.


## <a name="create-an-active-directory-connection"></a>Creare una connessione Active Directory

1. Dall'account NetApp fare clic su **Connessioni Active Directory** , quindi fare clic su **Aggiungi**.  

    ![Connessioni Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-connections.png)

2. Nella finestra Aggiungi Active Directory specificare le informazioni seguenti in base ai servizi di dominio che si vuole usare:  

    Per informazioni specifiche dei servizi di dominio usati, vedere [Decidere quali servizi di dominio usare](#decide-which-domain-services-to-use). 

    * **DNS primario**  
        Si tratta del DNS necessario per le operazioni di aggiunta al dominio Active Directory e di autenticazione SMB. 
    * **DNS secondario**   
        Si tratta del server DNS secondario per garantir la ridondanza dei servizi dei nomi. 
    * **Nome di dominio DNS di Active Directory**  
        Si tratta del nome di dominio di Active Directory Domain Services che si vuole aggiungere.
    * **Nome sito di AD**  
        Questo è il nome del sito a cui sarà limitata l'individuazione del controller di dominio. Deve corrispondere al nome del sito in Active Directory siti e servizi.
    * **Prefisso del server SMB (account computer)**  
        Questo è il prefisso di denominazione per l'account computer in Active Directory che verrà usato da Azure NetApp Files per la creazione di nuovi account.

        Se, ad esempio, lo standard di denominazione usato dall'organizzazione per i file server è NAS-01, NAS-02..., NAS-045, immettere "NAS" per il prefisso. 

        Il servizio creerà account computer aggiuntivi in Active Directory in base alle esigenze.

        > [!IMPORTANT] 
        > La ridenominazione del prefisso del server SMB dopo la creazione della connessione Active Directory è un'operazione che comporta problemi. Sarà necessario rimontare le condivisioni SMB esistenti dopo aver rinominato il prefisso del server SMB.

    * **Percorso unità organizzativa**  
        Si tratta del percorso LDAP per l'unità organizzativa (OU) in cui verranno creati gli account computer del server SMB, ad esempio, OU=second level, OU=first level. 

        Se si usa Azure NetApp Files con Azure Active Directory Domain Services, il percorso dell'unità organizzativa sarà `OU=AADDC Computers` quando si configura Active Directory per l'account NetApp.

    ![Aggiungi Active Directory](../media/azure-netapp-files/azure-netapp-files-join-active-directory.png)

    * **Crittografia AES**   
        Selezionare questa casella di controllo per abilitare la crittografia AES per un volume SMB. Vedere [requisiti per le connessioni Active Directory](#requirements-for-active-directory-connections) per i requisiti. 

        ![Crittografia AES Active Directory](../media/azure-netapp-files/active-directory-aes-encryption.png)

        La funzionalità di **crittografia AES** è attualmente disponibile in anteprima. Se è la prima volta che si usa questa funzionalità, registrare la funzionalità prima di usarla: 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFAesEncryption
        ```

        Verificare lo stato della registrazione della funzionalità: 

        > [!NOTE]
        > Il **RegistrationState** potrebbe trovarsi nello `Registering` stato per un massimo di 60 minuti prima di modificare in `Registered` . Prima di continuare, attendere che lo stato sia **registrato** .

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFAesEncryption
        ```
        
        È anche possibile usare i [comandi dell'interfaccia](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) `az feature register` della riga di comando di Azure e `az feature show` registrare la funzionalità e visualizzare lo stato della registrazione. 

    * **Firma LDAP**   
        Selezionare questa casella di controllo per abilitare la firma LDAP. Questa funzionalità consente ricerche LDAP sicure tra il servizio Azure NetApp Files e i [controller di dominio Active Directory Domain Services](/windows/win32/ad/active-directory-domain-services)specificati dall'utente. Per ulteriori informazioni, vedere [ADV190023 | Indicazioni Microsoft per l'abilitazione dell'associazione di canale LDAP e della firma LDAP](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190023).  

        ![Active Directory firma LDAP](../media/azure-netapp-files/active-directory-ldap-signing.png) 

        La funzionalità di **firma LDAP** è attualmente in anteprima. Se è la prima volta che si usa questa funzionalità, registrare la funzionalità prima di usarla: 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFLdapSigning
        ```

        Verificare lo stato della registrazione della funzionalità: 

        > [!NOTE]
        > Il **RegistrationState** potrebbe trovarsi nello `Registering` stato per un massimo di 60 minuti prima di modificare in `Registered` . Prima di continuare, attendere che lo stato sia **registrato** .

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFLdapSigning
        ```
        
        È anche possibile usare i [comandi dell'interfaccia](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) `az feature register` della riga di comando di Azure e `az feature show` registrare la funzionalità e visualizzare lo stato della registrazione. 

     * **Utenti dei criteri di backup**  
        È possibile includere account aggiuntivi che richiedono privilegi elevati per l'account computer creato per l'uso con Azure NetApp Files. Agli account specificati sarà consentito modificare le autorizzazioni NTFS a livello di file o di cartella. È ad esempio possibile specificare un account del servizio senza privilegi usato per la migrazione dei dati a una condivisione file SMB in Azure NetApp Files.  

        ![Utenti del criterio di backup Active Directory](../media/azure-netapp-files/active-directory-backup-policy-users.png)

        La funzionalità **utenti criteri di backup** è attualmente in anteprima. Se è la prima volta che si usa questa funzionalità, registrare la funzionalità prima di usarla: 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFBackupOperator
        ```

        Verificare lo stato della registrazione della funzionalità: 

        > [!NOTE]
        > Il **RegistrationState** potrebbe trovarsi nello `Registering` stato per un massimo di 60 minuti prima di modificare in `Registered` . Prima di continuare, attendere che lo stato sia **registrato** .

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFBackupOperator
        ```
        
        È anche possibile usare i [comandi dell'interfaccia](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) `az feature register` della riga di comando di Azure e `az feature show` registrare la funzionalità e visualizzare lo stato della registrazione. 

    * Credenziali, inclusi **nome utente** e **password**

        ![Credenziali Active Directory](../media/azure-netapp-files/active-directory-credentials.png)

3. Fare clic su **Accedi**.  

    Viene visualizzata la connessione Active Directory creata.

    ![Connessioni di Active Directory create](../media/azure-netapp-files/azure-netapp-files-active-directory-connections-created.png)

## <a name="add-an-smb-volume"></a>Aggiungere un volume SMB

1. Scegliere il pannello **Volumi** nel pannello Pool di capacità. 

    ![Passare a Volumi](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. Fare clic su **+ Aggiungi volume** per creare un volume.  
    Viene visualizzata la finestra Crea un volume.

3. Nella finestra Crea un volume fare clic su **Crea** e fornire informazioni per i campi seguenti nella scheda nozioni di base:   
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
        
        Se non è stata delegata una subnet, fare clic su **Crea nuovo** nella pagina Crea un volume. Nella pagina di creazione della subnet, specificare le informazioni relative alla stessa e selezionare **Microsoft.NetApp/volumi** per delegarla ad Azure NetApp Files. In ogni rete virtuale è possibile delegare una sola subnet ad Azure NetApp Files.   
 
        ![Crea un volume](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Creare una subnet](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

    * Se si desidera applicare un criterio snapshot esistente al volume, fare clic su **Mostra sezione avanzata** per espanderlo, specificare se si desidera nascondere il percorso dello snapshot e selezionare un criterio di snapshot nel menu a discesa. 

        Per informazioni sulla creazione di un criterio snapshot, vedere [Manage snapshot Policies](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies).

        ![Mostra selezione avanzata](../media/azure-netapp-files/volume-create-advanced-selection.png)

4. Fare clic su **Protocollo** e completare le informazioni seguenti:  
    * Selezionare **SMB** come tipo di protocollo per il volume. 
    * Selezionare la connessione **Active Directory** nell'elenco a discesa.
    * Specificare il nome del volume condiviso in **Nome condivisione**.

    ![Specificare il protocollo SMB](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. Fare clic su **Rivedi e crea** per esaminare i dettagli del volume.  Quindi fare clic su **Crea** per creare il volume SMB.

    Il volume creato viene visualizzato nella pagina Volumi. 
 
    Un volume eredita sottoscrizione, gruppo di risorse e attributi di posizione dal relativo pool di capacità. Per monitorare lo stato di distribuzione del volume, è possibile usare la scheda Notifiche.

## <a name="control-access-to-an-smb-volume"></a>Controllare l'accesso a un volume SMB  

L'accesso a un volume SMB viene gestito tramite le autorizzazioni.  

### <a name="share-permissions"></a>Autorizzazioni di condivisione  

Per impostazione predefinita, un nuovo volume dispone delle autorizzazioni di condivisione **Tutti/Controllo completo**. I membri del gruppo Domain Admins possono modificare le autorizzazioni di condivisione mediante Gestione computer nell'account computer usato per il volume Azure NetApp Files.

![Percorso di montaggio SMB](../media/azure-netapp-files/smb-mount-path.png) 
![Impostare le autorizzazioni di condivisione](../media/azure-netapp-files/set-share-permissions.png) 

### <a name="ntfs-file-and-folder-permissions"></a>Autorizzazioni per file e cartelle NTFS  

È possibile impostare le autorizzazioni per un file o una cartella usando la scheda **Sicurezza** delle proprietà dell'oggetto nel client SMB di Windows.
 
![Impostare le autorizzazioni per file e cartelle](../media/azure-netapp-files/set-file-folder-permissions.png) 

## <a name="next-steps"></a>Passaggi successivi  

* [Montare o smontare un volume per macchine virtuali Windows o Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Limiti delle risorse per Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Domande frequenti su SMB](./azure-netapp-files-faqs.md#smb-faqs)
* [Informazioni sull'integrazione delle reti virtuali per i servizi di Azure](../virtual-network/virtual-network-for-azure-services.md)
* [Installare una nuova foresta Active Directory usando l'interfaccia della riga di comando di Azure](/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm)
