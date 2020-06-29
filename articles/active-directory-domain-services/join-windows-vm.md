---
title: Aggiungere una macchina virtuale Windows Server a un dominio gestito di Azure AD Domain Services | Microsoft Docs
description: In questa esercitazione si apprenderà come aggiungere una macchina virtuale Windows Server a un dominio gestito di Azure Active Directory Domain Services.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: ac7af2f4500f6702dcacad546b0985e41159dc6e
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/12/2020
ms.locfileid: "84734674"
---
# <a name="tutorial-join-a-windows-server-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain"></a>Esercitazione: Aggiungere una macchina virtuale Windows a un dominio gestito di Azure Active Directory Domain Services

Azure Active Directory Domain Services (Azure AD DS) offre servizi di dominio gestiti, come l'aggiunta a un dominio, Criteri di gruppo, LDAP e l'autenticazione Kerberos/NTLM, completamente compatibili con Windows Server Active Directory. Con un dominio gestito di Azure Active Directory Domain Services, è possibile rendere disponibili alle macchine virtuali di Azure funzionalità per l'aggiunta a un dominio e la gestione. Questa esercitazione illustra come creare una macchina virtuale Windows Server e quindi aggiungerla a un dominio gestito.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare una macchina virtuale Windows Server
> * Connettere la macchina virtuale Windows Server a una rete virtuale di Azure
> * Aggiungere la macchina virtuale al dominio gestito

Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione, sono necessarie le risorse seguenti:

* Una sottoscrizione di Azure attiva.
    * Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un tenant di Azure Active Directory associato alla sottoscrizione, sincronizzato con una directory locale o con una directory solo cloud.
    * Se necessario, [creare un tenant di Azure Active Directory][create-azure-ad-tenant] o [associare una sottoscrizione di Azure al proprio account][associate-azure-ad-tenant].
* Un dominio gestito di Azure Active Directory Domain Services abilitato e configurato nel tenant di Azure AD.
    * Se necessario, [creare e configurare un dominio gestito di Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Un account utente che fa parte del dominio gestito.
    * Assicurarsi che sia stata eseguita la sincronizzazione degli hash delle password di Azure AD Connect o la reimpostazione della password self-service in modo che l'account sia in grado di accedere al dominio gestito.
* Un host Azure Bastion distribuito nella rete virtuale di Azure Active Directory Domain Services.
    * Se necessario, [creare un host Azure Bastion][azure-bastion].

Se si ha già una VM da aggiungere al dominio, passare alla sezione che descrive come [aggiungerla al dominio gestito](#join-the-vm-to-the-managed-domain).

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

In questa esercitazione si crea una macchina virtuale Windows Server da aggiungere al dominio gestito usando il portale di Azure. Per iniziare, accedere prima di tutto al [portale di Azure](https://portal.azure.com).

## <a name="create-a-windows-server-virtual-machine"></a>creare una macchina virtuale Windows Server

Per vedere come aggiungere un computer a un dominio gestito, verrà creata una VM Windows Server. Questa VM è connessa a una rete virtuale di Azure che fornisce la connettività al dominio gestito. La procedura di aggiunta a un dominio gestito è uguale a quella per un normale dominio di Active Directory Domain Services locale.

Se si ha già una VM da aggiungere al dominio, passare alla sezione che descrive come [aggiungerla al dominio gestito](#join-the-vm-to-the-managed-domain).

1. Nel menu del portale di Azure o dalla pagina **Home** selezionare **Crea una risorsa**.

1. In **Per iniziare** scegliere **Windows Server 2016 Datacenter**.

    ![Scegliere di creare una macchina virtuale Windows Server 2016 Datacenter nel portale di Azure](./media/join-windows-vm/select-vm-image.png)

1. Nella finestra **Nozioni di base** configurare le impostazioni di base per la macchina virtuale. Lasciare invariate le impostazioni predefinite per *Opzioni di disponibilità*, *Immagine* e *Dimensioni*.

    | Parametro            | Valore consigliato   |
    |----------------------|-------------------|
    | Resource group       | Selezionare o creare un gruppo di risorse, ad *esempio myResourceGroup* |
    | Nome macchina virtuale | Immettere un nome per la macchina virtuale, ad esempio *myVM* |
    | Region               | Scegliere l'area in cui creare la macchina virtuale, ad esempio *Stati Uniti orientali* |
    | Username             | Immettere un nome utente per l'account amministratore locale da creare nella macchina virtuale, ad esempio *azureuser* |
    | Password             | Immettere e confermare una password sicura per l'amministratore locale da creare nella macchina virtuale. Non specificare le credenziali di un account utente di dominio. |

1. Per impostazione predefinita, le macchine virtuali create in Azure sono accessibili da Internet tramite il protocollo RDP. Quando è abilitato il protocollo RDP, è probabile che si verifichino attacchi di accesso automatizzato, che possono disabilitare gli account con nomi comuni come *admin* o *administrator* a causa di più tentativi di accesso successivi non riusciti.

    Il protocollo RDP deve essere abilitato solo quando necessario e limitato a un set di intervalli di indirizzi IP autorizzati. Questa configurazione consente di migliorare la sicurezza della macchina virtuale e di ridurre l'area soggetta a potenziali attacchi. In alternativa, è possibile creare e usare un host Azure Bastion per consentire l'accesso solo dal portale di Azure tramite TLS. Nel passaggio successivo di questa esercitazione si userà un host Azure Bastion per connettersi in modo sicuro alla macchina virtuale.

    In **Porte in ingresso pubbliche** selezionare *Nessuna*.

1. Al termine, scegliere **Avanti: Dischi**.
1. Dal menu a discesa per **Tipo di disco del sistema operativo**, scegliere *SSD Standard* e quindi **Avanti: Rete**.
1. La VM deve connettersi a una subnet della rete virtuale di Azure in grado di comunicare con la subnet in cui viene distribuito il dominio gestito. È consigliabile distribuire un dominio gestito in una propria subnet dedicata. Non distribuire la macchina virtuale nella stessa subnet del dominio gestito.

    Esistono due modi principali per distribuire la macchina virtuale e connettersi a una subnet di rete virtuale appropriata:
    
    * Creare una subnet, o selezionarne una esistente, nella stessa rete virtuale in cui viene distribuito il dominio gestito.
    * Selezionare una subnet in una rete virtuale di Azure connessa alla subnet specificata nel passaggio precedente tramite [peering di rete virtuale di Azure][vnet-peering].
    
    Se si seleziona una subnet di rete virtuale non connessa alla subnet del dominio gestito, non è possibile aggiungere la VM al dominio gestito. Per questa esercitazione si creerà una nuova subnet nella rete virtuale di Azure.

    Nel riquadro **Rete** selezionare la rete virtuale in cui è distribuito il dominio gestito, ad esempio *aaads-vnet*
1. In questo esempio viene mostrata la subnet *aaads-subnet* esistente a cui è connesso il dominio gestito. Non connettere la macchina virtuale a questa subnet. Per creare una subnet per la macchina virtuale, selezionare **Gestisci configurazione subnet**.

    ![Scegliere di gestire la configurazione della subnet nel portale di Azure](./media/join-windows-vm/manage-subnet.png)

1. Nel menu sinistro della finestra della rete virtuale selezionare **Spazio degli indirizzi**. La rete virtuale viene creata con un unico spazio indirizzi, *10.0.2.0/24*, usato dalla subnet predefinita. È possibile che siano presenti anche altre subnet, ad esempio per i *carichi di lavoro* o Azure Bastion.

    Aggiungere un intervallo di indirizzi IP aggiuntivo alla rete virtuale. Le dimensioni di questo intervallo di indirizzi e l'intervallo di indirizzi IP effettivo da usare dipendono dalle altre risorse di rete già distribuite. L'intervallo di indirizzi IP non deve sovrapporsi agli intervalli di indirizzi esistenti nell'ambiente di Azure o in locale. Assicurarsi di scegliere dimensioni dell'intervallo di indirizzi IP sufficienti per il numero di VM che si prevede di distribuire nella subnet.

    Nell'esempio seguente viene aggiunto l'intervallo di indirizzi IP *10.0.5.0/24*. Al termine, selezionare **Salva**.

    ![Aggiungere un altro intervallo di indirizzi IP di rete virtuale nel portale di Azure](./media/join-windows-vm/add-vnet-address-range.png)

1. Successivamente, nel menu sinistro della finestra della rete virtuale selezionare **Subnet**, quindi scegliere **+ Subnet** per aggiungere una subnet.

1. Selezionare **+ Subnet** e quindi immettere un nome per la subnet, ad esempio *management*. Specificare un valore in **Intervallo di indirizzi (blocco CIDR)** , ad esempio *10.0.5.0/24*. Assicurarsi che questo intervallo di indirizzi IP non si sovrapponga ad altri intervalli di indirizzi di Azure o locali esistenti. Lasciare invariati i valori predefiniti delle altre opzioni e quindi scegliere **OK**.

    ![Creare una configurazione di subnet nel portale di Azure](./media/join-windows-vm/create-subnet.png)

1. Per la creazione della subnet sono necessari alcuni secondi. Dopo aver creato la subnet, selezionare la *X* per chiudere la finestra.
1. Tornare al riquadro **Rete** per creare una macchina virtuale e scegliere la subnet creata dal menu a discesa, ad esempio *management*. Anche in questo caso, assicurarsi di scegliere la subnet corretta e non distribuire la macchina virtuale nella stessa subnet del dominio gestito.
1. Per **Indirizzo IP pubblico** selezionare *Nessuno* dal menu a discesa, in quanto per connettersi alla gestione verrà usato Azure Bastion e non è quindi necessario un indirizzo IP pubblico assegnato.
1. Lasciare invariati i valori predefiniti delle altre opzioni e quindi scegliere **Gestione**.
1. Impostare **Diagnostica di avvio** su *Off*. Lasciare invariati i valori predefiniti delle altre opzioni e quindi scegliere **Rivedi e crea**.
1. Rivedere le impostazioni della macchina virtuale e quindi selezionare **Crea**.

La creazione della macchina virtuale richiede alcuni minuti. Il portale di Azure mostra lo stato della distribuzione. Quando la macchina virtuale è pronta, selezionare **Vai alla risorsa**.

![Passare alla risorsa Macchina virtuale nel portale di Azure dopo che è stata creata](./media/join-windows-vm/vm-created.png)

## <a name="connect-to-the-windows-server-vm"></a>Connettersi alla macchina virtuale Windows Server

Per connettersi in modo sicuro alle macchine virtuali, usare un host Azure Bastion. Con Azure Bastion,viene distribuito nella rete virtuale un host gestito che fornisce connessioni RDP o SSH basate sul Web alle macchine virtuali. Per le macchine virtuali non sono necessari indirizzi IP pubblici e non è necessario aprire regole del gruppo di sicurezza di rete per il traffico remoto esterno. È possibile connettersi alle macchine virtuali usando il portale di Azure dal Web browser in uso.

Per usare un host Bastion per connettersi alla macchina virtuale, seguire questa procedura:

1. Nel riquadro **Panoramica** della macchina virtuale selezionare **Connetti** e quindi **Bastion**.

    ![Connettersi alla macchina virtuale Windows tramite Bastion nel portale di Azure](./media/join-windows-vm/connect-to-vm.png)

1. Immettere le credenziali per la macchina virtuale specificata nella sezione precedente, quindi selezionare **Connetti**.

   ![Connettersi tramite l'host Bastion nel portale di Azure](./media/join-windows-vm/connect-to-bastion.png)

Se necessario, consentire al Web browser di aprire finestre popup per la visualizzazione della connessione Bastion. Per stabilire la connessione con la macchina virtuale sono necessari alcuni secondi.

## <a name="join-the-vm-to-the-managed-domain"></a>Aggiungere la macchina virtuale al dominio gestito

Una volta creata la macchina virtuale e dopo aver stabilito una connessione RDP basata sul Web tramite Azure Bastion, si aggiungerà la macchina virtuale Windows Server al dominio gestito. Questa procedura è identica a quella usata per connettere un computer a un normale dominio di Active Directory Domain Services locale.

1. Se **Server Manager** non viene aperto per impostazione predefinita quando si accede alla macchina virtuale, selezionare il menu **Start** e quindi scegliere **Server Manager**.
1. Nel riquadro sinistro della finestra **Server Manager** selezionare **Local Server**. Nell'area **Proprietà** del riquadro destro scegliere **Gruppo di lavoro**.

    ![Aprire Server Manager nella macchina virtuale e modificare la proprietà del gruppo di lavoro](./media/join-windows-vm/server-manager.png)

1. Nella finestra **Proprietà del sistema** selezionare **Cambia** per eseguire l'aggiunta al dominio.

    ![Scegliere di modificare le proprietà del gruppo di lavoro o del dominio](./media/join-windows-vm/change-domain.png)

1. Nella casella **Dominio** specificare il nome del dominio gestito, ad esempio *aaddscontoso.com*, e quindi selezionare **OK**.

    ![Specificare il dominio gestito per l'aggiunta](./media/join-windows-vm/join-domain.png)

1. Immettere le credenziali di dominio per l'aggiunta al dominio. Usare le credenziali di un utente che fa parte del dominio gestito. L'account deve far parte del dominio gestito o del tenant di Azure AD. Gli account di directory esterne associate al tenant di Azure AD non possono eseguire correttamente l'autenticazione durante il processo di aggiunta al dominio. Le credenziali dell'account possono essere specificate in uno dei modi seguenti:

    * **Formato UPN** (consigliato): immettere il suffisso UPN (User Principal Name) per l'account utente, come configurato in Azure AD. Ad esempio, il suffisso UPN dell'utente *contosoadmin* sarà `contosoadmin@aaddscontoso.onmicrosoft.com`. Esistono un paio di casi d'uso comuni in cui il formato UPN può essere usato in modo affidabile per accedere al dominio in sostituzione del formato *SAMAccountName*:
        * Se il prefisso UPN di un utente è lungo, ad esempio *nomeutentetroppolungo*, è possibile che venga generato automaticamente il formato *SAMAccountName*.
        * Se più utenti hanno lo stesso prefisso UPN nel tenant di Azure AD, ad esempio *utente*, è possibile che venga generato automaticamente il formato *SAMAccountName*.
    * **Formato SAMAccountName**: immettere il nome dell'account nel formato *SAMAccountName*. Ad esempio, il formato *SAMAccountName* dell'utente *contosoadmin* sarà `AADDSCONTOSO\contosoadmin`.

1. L'aggiunta al dominio gestito richiede alcuni secondi. Al termine dell'operazione, viene visualizzato il seguente messaggio di benvenuto nel dominio:

    ![Messaggio di benvenuto al dominio](./media/join-windows-vm/join-domain-successful.png)

    Selezionare **OK** per continuare.

1. Per completare la procedura di aggiunta al dominio gestito, riavviare la macchina virtuale.

> [!TIP]
> È possibile aggiungere una macchina virtuale a un dominio usando PowerShell con il cmdlet [Add-Computer][add-computer]. Nell'esempio seguente la macchina virtuale viene aggiunta al dominio *AADDSCONTOSO* e quindi riavviata. Quando richiesto, immettere le credenziali di un utente che fa parte del dominio gestito:
>
> `Add-Computer -DomainName AADDSCONTOSO -Restart`
>
> Per aggiungere al dominio una macchina virtuale senza connetterla e configurare manualmente la connessione, è possibile usare il cmdlet [Set-AzVmAdDomainExtension][set-azvmaddomainextension] di Azure PowerShell.

Dopo il riavvio della macchina virtuale Windows Server, i criteri applicati nel dominio gestito verranno inviati tramite push nella macchina virtuale. È ora possibile accedere alla macchina virtuale Windows Server VM anche usando le credenziali di dominio appropriate.

## <a name="clean-up-resources"></a>Pulire le risorse

Nell'esercitazione successiva questa VM Windows Server verrà usata per installare gli strumenti di gestione che consentono di amministrare il dominio gestito. Se non si vuole proseguire con questa serie di esercitazioni, vedere la procedura di pulizia descritta di seguito per [eliminare la macchina virtuale](#delete-the-vm). In caso contrario, [continuare con l'esercitazione successiva](#next-steps).

### <a name="un-join-the-vm-from-the-managed-domain"></a>Annullare l'aggiunta della VM al dominio gestito

Per rimuovere la VM dal dominio gestito, seguire di nuovo la procedura di [aggiunta al dominio](#join-the-vm-to-the-managed-domain). Invece di aggiungere il dominio gestito, scegliere di aggiungere un gruppo di lavoro, ad esempio il gruppo di lavoro predefinito *WORKGROUP*. Dopo il riavvio della macchina virtuale, l'oggetto computer viene rimosso dal dominio gestito.

Se si [elimina la macchina virtuale](#delete-the-vm) senza rimuoverla dal dominio, in Azure Active Directory Domain Services rimarrà un oggetto computer orfano.

### <a name="delete-the-vm"></a>Eliminare la macchina virtuale

Se non si intende più usare la macchina virtuale Windows Server, eliminarla seguendo questa procedura:

1. Dal menu a sinistra selezionare **Gruppi di risorse**
1. Scegliere il gruppo di risorse, ad esempio *myResourceGroup*.
1. Scegliere la macchina virtuale, ad esempio *myVM*, e quindi selezionare **Elimina**. Selezionare **Sì** per confermare l'eliminazione della risorsa. Per l'eliminazione della macchina virtuale sono necessari alcuni minuti.
1. Dopo aver eliminato la macchina virtuale, selezionare il disco del sistema operativo, la scheda di interfaccia di rete e le eventuali altre risorse con il prefisso *myVM-* ed eliminarle.

## <a name="troubleshoot-domain-join-issues"></a>Risolvere i problemi relativi all'aggiunta al dominio

La macchina virtuale Windows Server dovrebbe essere aggiunta al dominio gestito nello stesso modo in cui un normale computer locale viene aggiunto a un dominio di Active Directory Domain Services. Se la macchina virtuale Windows Server non può essere aggiunta al dominio gestito, significa che si è verificato un problema di connettività o di credenziali. Vedere le sezioni di risoluzione dei problemi riportate di seguito per aggiungere correttamente la macchina virtuale al dominio gestito.

### <a name="connectivity-issues"></a>Problemi di connettività

Se non viene visualizzato un messaggio di richiesta di credenziali per l'aggiunta al dominio, significa che si è verificato un problema di connettività. La macchina virtuale non riesce a raggiungere il dominio gestito nella rete virtuale.

Dopo aver provato ognuno di questi passaggi di risoluzione dei problemi, provare ad aggiungere di nuovo la macchina virtuale Windows Server al dominio gestito.

* Verificare che la macchina virtuale sia connessa alla stessa rete virtuale in cui è abilitato Azure Active Directory Domain Services oppure che disponga di una connessione di rete con peering.
* Provare a effettuare il ping del nome DNS del dominio gestito, ad esempio `ping aaddscontoso.com`.
    * Se la richiesta ping ha esito negativo, provare a effettuare il ping degli indirizzi IP per il dominio gestito, ad esempio `ping 10.0.0.4`. Quando si seleziona il dominio gestito nell'elenco delle risorse di Azure, l'indirizzo IP relativo all'ambiente viene visualizzato nella pagina *Proprietà*.
    * Se si riesce a effettuare il ping dell'indirizzo IP ma non del dominio, la configurazione del server DNS potrebbe non essere valida. Verificare che gli indirizzi IP del dominio gestito siano configurati come server DNS per la rete virtuale.
* Provare a scaricare la cache del resolver DNS sulla macchina virtuale usando il comando `ipconfig /flushdns`.

### <a name="credentials-related-issues"></a>Problemi correlati alle credenziali

Se si riceve la richiesta di immettere credenziali per l'aggiunta al dominio ma dopo averle immesse si riceve un errore, la VM è in grado di connettersi al dominio gestito, ma le credenziali specificate non consentono di aggiungerla.

Dopo aver provato ognuno di questi passaggi di risoluzione dei problemi, provare ad aggiungere di nuovo la macchina virtuale Windows Server al dominio gestito.

* Assicurarsi che l'account utente specificato appartenga al dominio gestito.
* Verificare che l'account faccia parte del dominio gestito del tenant di Azure AD. Gli account di directory esterne associate al tenant di Azure AD non possono eseguire correttamente l'autenticazione durante il processo di aggiunta al dominio.
* Provare a usare il formato UPN per specificare le credenziali, ad esempio `contosoadmin@aaddscontoso.onmicrosoft.com`. Se sono presenti diversi utenti con lo stesso prefisso UPN nel tenant o se il prefisso UPN è troppo lungo, è possibile che venga generato automaticamente il formato *SAMAccountName*. In questi casi, il formato *SAMAccountName* per l'account può essere diverso da quello previsto o usato nel dominio locale.
* Controllare di avere [abilitato la sincronizzazione password][password-sync] nel dominio gestito. Senza questo passaggio di configurazione, gli hash delle password richiesti non saranno presenti nel dominio gestito per autenticare correttamente il tentativo di accesso.
* Attendere il completamento della sincronizzazione delle password. Quando viene cambiata la password di un account utente, una sincronizzazione automatica in background di Azure AD la aggiorna in Azure AD DS. La disponibilità della password per l'aggiunta a un dominio richiede tempo.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

> [!div class="checklist"]
> * Creare una macchina virtuale Windows Server
> * Connettersi alla macchina virtuale Windows Server in una rete virtuale di Azure
> * Aggiungere la macchina virtuale al dominio gestito

Per amministrare il dominio gestito, configurare una macchina virtuale di gestione usando il Centro di amministrazione di Active Directory.

> [!div class="nextstepaction"]
> [Installare gli strumenti di amministrazione in una macchina virtuale di gestione](tutorial-create-management-vm.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[vnet-peering]: ../virtual-network/virtual-network-peering-overview.md
[password-sync]: active-directory-ds-getting-started-password-sync.md
[add-computer]: /powershell/module/microsoft.powershell.management/add-computer
[azure-bastion]: ../bastion/bastion-create-host-portal.md
[set-azvmaddomainextension]: /powershell/module/az.compute/set-azvmaddomainextension
