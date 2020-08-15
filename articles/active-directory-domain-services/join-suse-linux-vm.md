---
title: Aggiungere una macchina virtuale SLE a Azure AD Domain Services | Microsoft Docs
description: Informazioni su come configurare e aggiungere una macchina virtuale SUSE Linux Enterprise a un dominio gestito Azure AD Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 08/12/2020
ms.author: iainfou
ms.openlocfilehash: 9f50be95e456802c6ad403acd6a2f539780e53a2
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/15/2020
ms.locfileid: "88251183"
---
# <a name="join-a-suse-linux-enterprise-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain"></a>Aggiungere una macchina virtuale SUSE Linux Enterprise a un dominio gestito Azure Active Directory Domain Services

Per consentire agli utenti di accedere a macchine virtuali (VM) in Azure usando un unico set di credenziali, è possibile aggiungere macchine virtuali a un dominio gestito di Azure Active Directory Domain Services (Azure AD DS). Quando si aggiunge una macchina virtuale a un dominio gestito di Azure AD DS, è possibile usare gli account utente e le credenziali del dominio per l'accesso e la gestione dei server. Le appartenenze ai gruppi dal dominio gestito vengono applicate anche per consentire di controllare l'accesso ai file o ai servizi nella macchina virtuale.

Questo articolo illustra come aggiungere una VM SUSE Linux Enterprise (SLE) a un dominio gestito.

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione, sono necessari i privilegi e le risorse seguenti:

* Una sottoscrizione di Azure attiva.
    * Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un tenant di Azure Active Directory associato alla sottoscrizione, sincronizzato con una directory locale o con una directory solo cloud.
    * Se necessario, [creare un tenant di Azure Active Directory][create-azure-ad-tenant] o [associare una sottoscrizione di Azure al proprio account][associate-azure-ad-tenant].
* Un dominio gestito di Azure Active Directory Domain Services abilitato e configurato nel tenant di Azure AD.
    * Se necessario, seguire la prima esercitazione per [creare e configurare un dominio gestito di Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Un account utente che fa parte del dominio gestito.

## <a name="create-and-connect-to-a-sle-linux-vm"></a>Creare e connettersi a una macchina virtuale SLE Linux

Se si dispone di una macchina virtuale SLE Linux esistente in Azure, connettersi con SSH, quindi continuare con il passaggio successivo per [iniziare a configurare la macchina virtuale](#configure-the-hosts-file).

Se è necessario creare una VM SLE Linux o si vuole creare una macchina virtuale di test da usare con questo articolo, è possibile usare uno dei metodi seguenti:

* [Azure portal](../virtual-machines/linux/quick-create-portal.md)
* [Interfaccia della riga di comando di Azure](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

Quando si crea la VM, prestare attenzione alle impostazioni della rete virtuale per assicurarsi che la macchina virtuale sia in grado di comunicare con il dominio gestito:

* Distribuire la macchina virtuale nella stessa rete virtuale con peering, in cui è stata abilitata Azure AD Domain Services.
* Distribuire la macchina virtuale in una subnet diversa da quella Azure AD Domain Services dominio gestito.

Dopo aver distribuito la macchina virtuale, seguire la procedura per connettersi alla macchina virtuale tramite SSH.

## <a name="configure-the-hosts-file"></a>Configurare il file hosts

Per assicurarsi che il nome host della macchina virtuale sia configurato correttamente per il dominio gestito, modificare *il file hosts e impostare il nome* host:

```console
sudo vi /etc/hosts
```

Nel file *host* aggiornare l'indirizzo *localhost* . Nell'esempio seguente:

* *aaddscontoso.com* è il nome di dominio DNS del dominio gestito.
* *Linux-q2gr* è il nome host della macchina virtuale SLE che si sta aggiungendo al dominio gestito.

Aggiornare questi nomi con valori personalizzati:

```console
127.0.0.1 linux-q2gr linux-q2gr.aaddscontoso.com
```

Al termine, salvare e chiudere il file *host* usando il `:wq` comando dell'editor.

## <a name="join-vm-to-the-managed-domain-using-sssd"></a>Aggiungere una macchina virtuale al dominio gestito usando SSSD

Per aggiungere il dominio gestito usando **SSSD** e il modulo *gestione accesso utenti* di YaST, seguire questa procedura:

1. Installare il modulo YaST per la *gestione dell'accesso utente* :

    ```bash
    sudo zypper install yast2-auth-client
    ```

1. Aprire YaST.

1. Per usare correttamente l'individuazione automatica DNS in un secondo momento, configurare gli indirizzi IP del dominio gestito (il *server Active Directory*) come server dei nomi per il client.

    In YaST selezionare **sistema > impostazioni di rete**.

1. Selezionare la scheda *hostname/DNS* , quindi immettere gli indirizzi IP del dominio gestito nella casella di testo *nome server 1*. Questi indirizzi IP vengono visualizzati nella finestra *Proprietà* della portale di Azure per il dominio gestito, ad esempio *10.0.2.4* e *10.0.2.5*.

    Aggiungere gli indirizzi IP del dominio gestito, quindi selezionare **OK**.

1. Dalla finestra principale di YaST scegliere *Network Services*  >  *gestione accesso utenti*servizi di rete.

    Il modulo si apre con una panoramica che mostra le diverse proprietà di rete del computer e il metodo di autenticazione attualmente in uso, come illustrato nello screenshot di esempio seguente:

    ![Schermata di esempio della finestra di gestione degli accessi utente in YaST](./media/join-suse-linux-vm/overview-window.png)

    Per avviare la modifica, selezionare **Cambia impostazioni**.

Per aggiungere la macchina virtuale al dominio gestito, attenersi alla procedura seguente:

1. Nella finestra di dialogo selezionare **Aggiungi dominio**.

1. Specificare il *nome di dominio*corretto, ad esempio *aaddscontoso.com*, quindi specificare i servizi da usare per l'autenticazione e i dati di identità. Selezionare *Microsoft Active Directory* per entrambi.

    Assicurarsi che sia selezionata l'opzione *Abilita il dominio* .

1. Al termine, selezionare **OK**.

1. Accettare le impostazioni predefinite nella finestra di dialogo seguente, quindi fare clic su **OK**.

1. La VM Installa software aggiuntivo in base alle esigenze, quindi verifica se il dominio gestito è disponibile.

    Se tutto è corretto, viene visualizzata la finestra di dialogo di esempio seguente per indicare che la macchina virtuale ha individuato il dominio gestito ma che *non è ancora stato registrato*.

    ![Schermata di esempio della finestra di registrazione Active Directory in YaST](./media/join-suse-linux-vm/enroll-window.png)

1. Nella finestra di dialogo specificare il *nome utente* e la *password* di un utente che fa parte del dominio gestito. Se necessario, [aggiungere un account utente a un gruppo in Azure ad](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md).

    Per assicurarsi che il dominio corrente sia abilitato per Samba, attivare *Sovrascrivi configurazione Samba per usare questo annuncio*.

1. Per eseguire la registrazione, fare clic su **OK**.

1. Viene visualizzato un messaggio per confermare che la registrazione è stata completata. Per terminare, fare clic su **OK**.

Dopo che la macchina virtuale è stata registrata nel dominio gestito, configurare il client usando *Gestisci accesso utente del dominio*, come illustrato nella schermata di esempio seguente:

![Screenshot di esempio della finestra Gestisci accesso utente del dominio in YaST](./media/join-suse-linux-vm/manage-domain-user-logon-window.png)

1. Per consentire l'accesso usando i dati forniti dal dominio gestito, selezionare la casella *Consenti accesso utente del dominio*.

1. Facoltativamente, in *Abilita origine dati dominio*selezionare origini dati aggiuntive in base alle esigenze dell'ambiente. Queste opzioni includono gli utenti autorizzati a usare **sudo** o le unità di rete disponibili.

1. Per consentire agli utenti del dominio gestito di avere Home directory nella macchina virtuale, selezionare la casella per *Crea directory Home*.

1. Dalla barra laterale selezionare **Opzioni servizio › opzione nome**, quindi *Opzioni estese*. Da tale finestra selezionare *fallback_homedir* o *override_homedir*, quindi selezionare **Aggiungi**.

1. Specificare un valore per il percorso della Home Directory. Per fare in modo che Home directory segua il formato di */home/user_name*, utilizzare */Home/%u*. Per ulteriori informazioni sulle possibili variabili, vedere la sezione override_homedir della pagina dell'uomo SSSD. conf ( `man 5 sssd.conf` ). *override_homedir*

1. Selezionare **OK**.

1. Per salvare le modifiche, selezionare **OK**. Assicurarsi quindi che i valori visualizzati ora siano corretti. Per uscire dalla finestra di dialogo, selezionare **Annulla**.

1. Se si prevede di eseguire contemporaneamente SSSD e Winbind, ad esempio quando si entra in un join tramite SSSD, ma in seguito si esegue un file server Samba, il *metodo Kerberos* dell'opzione Samba deve essere impostato su *Secrets e keytab* in SMB. conf. L'opzione SSSD *ad_update_samba_machine_account_password* deve anche essere impostata su *true* in SSSD. conf. Queste opzioni impediscono al sistema keytab di uscire dalla sincronizzazione.

## <a name="join-vm-to-the-managed-domain-using-winbind"></a>Aggiungere una macchina virtuale al dominio gestito usando Winbind

Per aggiungere il dominio gestito usando **Winbind** e il modulo di *appartenenza al dominio di Windows* di YaST, seguire questa procedura:

1. In YaST selezionare **servizi di rete > appartenenza al dominio Windows**.

1. Immettere il dominio da aggiungere al *dominio o al gruppo* di lavoro nella schermata *appartenenza al dominio di Windows* . Immettere il nome di dominio gestito, ad esempio *aaddscontoso.com*.

    ![Schermata di esempio della finestra appartenenza al dominio Windows in YaST](./media/join-suse-linux-vm/samba-client-window.png)

1. Per usare l'origine SMB per l'autenticazione Linux, selezionare l'opzione *Usa informazioni SMB per l'autenticazione Linux*.

1. Per creare automaticamente una home directory locale per gli utenti di un dominio gestito nella macchina virtuale, selezionare l'opzione *Crea Home Directory all'accesso*.

1. Selezionare l'opzione per *l'autenticazione offline* per consentire agli utenti del dominio di accedere anche se il dominio gestito è temporaneamente non disponibile.

1. Se si desidera modificare gli intervalli di UID e GID per gli utenti e i gruppi di Samba, selezionare *Impostazioni avanzate*.

1. Configurare la sincronizzazione dell'ora NTP per il dominio gestito selezionando *configurazione NTP*. Immettere gli indirizzi IP del dominio gestito. Questi indirizzi IP vengono visualizzati nella finestra *Proprietà* della portale di Azure per il dominio gestito, ad esempio *10.0.2.4* e *10.0.2.5*.

1. Selezionare **OK** e confermare l'aggiunta al dominio quando richiesto.

1. Specificare la password per un amministratore nel dominio gestito e fare clic su **OK**.

    ![Schermata di esempio della richiesta di conferma della finestra di dialogo di autenticazione quando si aggiunge una macchina virtuale SLE al dominio gestito](./media/join-suse-linux-vm/domain-join-authentication-prompt.png)

Dopo aver aggiunto il dominio gestito, è possibile accedervi dalla workstation usando la gestione schermo del desktop o la console.

## <a name="allow-password-authentication-for-ssh"></a>Consenti l'autenticazione tramite password per SSH

Per impostazione predefinita, gli utenti possono accedere a una VM solo usando l'autenticazione basata su chiave pubblica SSH. L'autenticazione basata su password ha esito negativo. Quando si aggiunge la macchina virtuale a un dominio gestito, è necessario che gli account di dominio usino l'autenticazione basata su password. Aggiornare la configurazione SSH per consentire l'autenticazione basata su password, come indicato di seguito.

1. Aprire il file di *sshd_conf* con un editor:

    ```console
    sudo vi /etc/ssh/sshd_config
    ```

1. Aggiornare la riga per *PasswordAuthentication* a *Sì*:

    ```console
    PasswordAuthentication yes
    ```

    Al termine, salvare e chiudere il file di *sshd_conf* usando il `:wq` comando dell'editor.

1. Per applicare le modifiche e consentire agli utenti di accedere con una password, riavviare il servizio SSH:

    ```console
    sudo systemctl restart sshd
    ```

## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>Concedere i privilegi sudo al gruppo "Amministratori di AAD DC"

Per concedere ai membri degli *amministratori di AAD DC* i privilegi amministrativi per la VM SLE, aggiungere una voce a */etc/sudoers*. Una volta aggiunti, i membri del gruppo *AAD DC Administrators* possono usare il `sudo` comando nella macchina virtuale SLE.

1. Aprire il file *sudoers* per la modifica:

    ```console
    sudo visudo
    ```

1. Aggiungere la voce seguente alla fine del file */etc/sudoers* . Il gruppo *AAD DC Administrators* contiene uno spazio vuoto nel nome, quindi includere il carattere di escape barra rovesciata nel nome del gruppo. Aggiungere il proprio nome di dominio, ad esempio *aaddscontoso.com*:

    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators@aaddscontoso.com ALL=(ALL) NOPASSWD:ALL
    ```

    Al termine, salvare e chiudere l'editor utilizzando il `:wq` comando dell'editor.

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>Accedere alla macchina virtuale usando un account di dominio

Per verificare che la macchina virtuale sia stata aggiunta correttamente al dominio gestito, avviare una nuova connessione SSH usando un account utente di dominio. Verificare che sia stata creata una home directory e che sia stata applicata l'appartenenza al gruppo dal dominio.

1. Creare una nuova connessione SSH dalla console di. Usare un account di dominio appartenente al dominio gestito usando il `ssh -l` comando, ad esempio `contosoadmin@aaddscontoso.com` e quindi immettere l'indirizzo della macchina virtuale, ad esempio *Linux-q2gr.aaddscontoso.com*. Se si usa la Azure Cloud Shell, usare l'indirizzo IP pubblico della macchina virtuale anziché il nome DNS interno.

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com linux-q2gr.aaddscontoso.com
    ```

2. Dopo aver eseguito la connessione alla macchina virtuale, verificare che la home directory sia stata inizializzata correttamente:

    ```console
    pwd
    ```

    Si dovrebbe trovarsi nella directory */Home* con la propria directory che corrisponde all'account utente.

3. Verificare ora che le appartenenze ai gruppi siano state risolte correttamente:

    ```console
    id
    ```

    Le appartenenze a gruppi verranno visualizzate dal dominio gestito.

4. Se è stato effettuato l'accesso alla macchina virtuale come membro del gruppo di *amministratori di AAD DC* , verificare che sia possibile usare correttamente il `sudo` comando:

    ```console
    sudo zypper update
    ```

## <a name="next-steps"></a>Passaggi successivi

Se si verificano problemi durante la connessione della macchina virtuale al dominio gestito o l'accesso con un account di dominio, vedere [risoluzione dei](join-windows-vm.md#troubleshoot-domain-join-issues)problemi di aggiunta a un dominio.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
