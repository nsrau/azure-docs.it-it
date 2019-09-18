---
title: Aggiungere una macchina virtuale Ubuntu a Azure AD Domain Services | Microsoft Docs '
description: Informazioni su come configurare e aggiungere un Ubuntu Linux macchina virtuale a un dominio gestito da Azure AD Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 804438c4-51a1-497d-8ccc-5be775980203
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 09/15/2019
ms.author: iainfou
ms.openlocfilehash: e92327323f632f6b922e3eb948df75bb3666e2a9
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71075370"
---
# <a name="join-an-ubuntu-linux-virtual-machine-to-an-azure-ad-domain-services-managed-domain"></a>Aggiungere una macchina virtuale Ubuntu Linux a un dominio gestito Azure AD Domain Services

Per consentire agli utenti di accedere a macchine virtuali (VM) in Azure usando un unico set di credenziali, è possibile aggiungere macchine virtuali a un dominio gestito di Azure Active Directory Domain Services (AD DS). Quando si aggiunge una macchina virtuale a un dominio gestito di Azure AD DS, è possibile usare gli account utente e le credenziali del dominio per l'accesso e la gestione dei server. Vengono inoltre applicate le appartenenze ai gruppi dal dominio gestito Azure AD DS per consentire il controllo dell'accesso ai file o ai servizi nella macchina virtuale.

Questo articolo illustra come aggiungere una macchina virtuale Ubuntu Linux a un dominio gestito di Azure AD DS.

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione, sono necessari i privilegi e le risorse seguenti:

* Una sottoscrizione di Azure attiva.
    * Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un tenant di Azure Active Directory associato alla sottoscrizione, sincronizzato con una directory locale o con una directory solo cloud.
    * Se necessario, [creare un tenant di Azure Active Directory][create-azure-ad-tenant] o [associare una sottoscrizione di Azure al proprio account][associate-azure-ad-tenant].
* Un dominio gestito di Azure Active Directory Domain Services abilitato e configurato nel tenant di Azure AD.
    * Se necessario, seguire la prima esercitazione per [creare e configurare un'istanza di Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Un account utente membro del gruppo di *amministratori dei controller di dominio di Azure AD* nel tenant di Azure AD.

## <a name="create-and-connect-to-an-ubuntu-linux-vm"></a>Creare e connettersi a una macchina virtuale Ubuntu Linux

Se si dispone di una macchina virtuale Ubuntu Linux esistente in Azure, connettersi con SSH, quindi continuare con il passaggio successivo per [avviare la configurazione della macchina virtuale](#configure-the-hosts-file).

Se è necessario creare una macchina virtuale Ubuntu Linux o si vuole creare una macchina virtuale di test da usare con questo articolo, è possibile usare uno dei metodi seguenti:

* [Portale di Azure](../virtual-machines/linux/quick-create-portal.md)
* [Interfaccia della riga di comando di Azure](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

Quando si crea la VM, prestare attenzione alle impostazioni della rete virtuale per assicurarsi che la macchina virtuale sia in grado di comunicare con il dominio gestito di Azure AD DS:

* Distribuire la macchina virtuale nella stessa rete virtuale con peering, in cui è stata abilitata Azure AD Domain Services.
* Distribuire la macchina virtuale in una subnet diversa da quella dell'istanza di Azure AD Domain Services.

Dopo aver distribuito la macchina virtuale, seguire la procedura per connettersi alla macchina virtuale tramite SSH.

## <a name="configure-the-hosts-file"></a>Configurare il file hosts

Per assicurarsi che il nome host della macchina virtuale sia configurato correttamente per il dominio gestito, modificare *il file hosts e impostare il nome* host:

```console
sudo vi /etc/hosts
```

Nel file *host* aggiornare l'indirizzo *localhost* . Nell'esempio seguente:

* *contoso.com* è il nome di dominio DNS del dominio gestito di Azure AD DS.
* *Ubuntu* è il nome host della macchina virtuale Ubuntu che si sta aggiungendo al dominio gestito.

Aggiornare questi nomi con valori personalizzati:

```console
127.0.0.1 ubuntu.contoso.com ubuntu
```

Al termine, salvare e chiudere il file *host* usando il `:wq` comando dell'editor.

## <a name="install-required-packages"></a>Installare i pacchetti necessari

La VM necessita di alcuni pacchetti aggiuntivi per aggiungere la macchina virtuale al dominio gestito di Azure AD DS. Per installare e configurare questi pacchetti, aggiornare e installare gli strumenti di aggiunta al dominio usando`apt-get`

Durante l'installazione di Kerberos, il pacchetto *krb5-User* richiede il nome dell'area di autenticazione in tutti i caratteri maiuscoli. Ad esempio, se il nome del dominio gestito di Azure AD DS è *contoso.com*, immettere *contoso.com* come area di autenticazione. L'installazione scrive le `[realm]` sezioni `[domain_realm]` e nel file di configurazione */etc/krb5.conf* . Assicurarsi di specificare l'area di autenticazione in MAIUSCOLo:

```console
sudo apt-get update
sudo apt-get install krb5-user samba sssd sssd-tools libnss-sss libpam-sss ntp ntpdate realmd adcli
```

## <a name="configure-network-time-protocol-ntp"></a>Configurare il protocollo NTP (Network Time Protocol)

Per il corretto funzionamento della comunicazione del dominio, la data e l'ora della VM Ubuntu devono essere sincronizzate con il dominio gestito di Azure AD DS. Aggiungere il nome host NTP del dominio gestito di Azure AD DS al file */etc/ntp.conf* .

1. Aprire il file *NTP. conf* con un editor:

    ```console
    sudo vi /etc/ntp.conf
    ```

1. Nel file *NTP. conf* creare una riga per aggiungere il nome DNS del dominio gestito di Azure AD DS. Nell'esempio seguente viene aggiunta una voce per *contoso.com* . Usare il proprio nome DNS:

    ```console
    server contoso.com
    ```

    Al termine, salvare e chiudere il file *NTP. conf* usando il `:wq` comando dell'editor.

1. Per assicurarsi che la macchina virtuale sia sincronizzata con il dominio gestito di Azure AD DS, sono necessari i passaggi seguenti:

    * Arrestare il server NTP
    * Aggiornare la data e l'ora dal dominio gestito
    * Avviare il servizio NTP

    Per completare questi passaggi, eseguire i comandi seguenti. Usare il proprio nome DNS con il `ntpdate` comando:

    ```console
    sudo systemctl stop ntp
    sudo ntpdate contoso.com
    sudo systemctl start ntp
    ```

## <a name="join-vm-to-the-managed-domain"></a>Aggiungere una macchina virtuale al dominio gestito

Ora che i pacchetti necessari sono installati nella macchina virtuale e NTP è configurato, aggiungere la macchina virtuale al dominio gestito di Azure AD DS.

1. Usare il `realm discover` comando per individuare il dominio gestito di Azure AD DS. Nell'esempio seguente viene individuato il *contoso.com*dell'area di autenticazione. Specificare il proprio nome di dominio gestito di Azure AD DS in tutte le lettere maiuscole:

    ```console
    sudo realm discover CONTOSO.COM
    ```

   Se il `realm discover` comando non riesce a trovare il dominio gestito di Azure AD DS, esaminare i passaggi per la risoluzione dei problemi seguenti:

    * Verificare che il dominio sia raggiungibile dalla macchina virtuale. Provare `ping contoso.com` a verificare se viene restituita una risposta positiva.
    * Verificare che la macchina virtuale sia distribuita nello stesso o in una rete virtuale con peering in cui è disponibile il dominio gestito di Azure AD DS.
    * Verificare che le impostazioni del server DNS per la rete virtuale siano state aggiornate in modo che puntino ai controller di dominio del dominio gestito Azure AD DS.

1. A questo punto, inizializzare Kerberos usando il `kinit` comando. Specificare un utente appartenente al gruppo di *amministratori di AAD DC* . Se necessario, [aggiungere un account utente a un gruppo in Azure ad](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md).

    Anche in questo caso, è necessario immettere il nome di dominio gestito Azure AD DS in tutti i caratteri maiuscoli. Nell'esempio seguente viene usato l'account denominato `contosoadmin@contoso.com` per inizializzare Kerberos. Immettere il proprio account utente membro del gruppo di amministratori di *AAD DC* :

    ```console
    kinit contosoadmin@CONTOSO.COM
    ```

1. Aggiungere infine il computer al dominio gestito di Azure AD DS usando il `realm join` comando. Usare lo stesso account utente membro del gruppo *AAD DC Administrators* specificato nel `kinit` `contosoadmin@CONTOSO.COM`comando precedente, ad esempio:

    ```console
    sudo realm join --verbose CONTOSO.COM -U 'contosoadmin@CONTOSO.COM' --install=/
    ```

Per aggiungere la macchina virtuale al dominio gestito di Azure AD DS sono necessari alcuni minuti. L'output di esempio seguente mostra che la macchina virtuale è stata aggiunta correttamente al dominio gestito di Azure AD DS:

```output
Successfully enrolled machine in realm
```

Se la macchina virtuale non riesce a completare il processo di aggiunta al dominio, verificare che il gruppo di sicurezza di rete della macchina virtuale consenta il traffico Kerberos in uscita sulla porta TCP + UDP 464 alla subnet della rete virtuale per il dominio gestito di Azure AD DS.

## <a name="update-the-sssd-configuration"></a>Aggiornare la configurazione di SSSD

Uno dei pacchetti installati in un passaggio precedente è stato per il daemon di servizi di sicurezza di sistema (SSSD). Quando un utente tenta di accedere a una macchina virtuale usando le credenziali di dominio, SSSD inoltra la richiesta a un provider di autenticazione. In questo scenario, SSSD USA Azure AD DS per autenticare la richiesta.

1. Aprire il file *SSSD. conf* con un editor:

    ```console
    sudo vi /etc/sssd/sssd.conf
    ```

1. Impostare come commento la riga per *use_fully_qualified_names* come indicato di seguito:

    ```console
    # use_fully_qualified_names = True
    ```

    Al termine, salvare e chiudere il file *SSSD. conf* usando il `:wq` comando dell'editor.

1. Per applicare la modifica, riavviare il servizio SSSD:

    ```console
    sudo service sssd restart
    ```

## <a name="configure-user-account-and-group-settings"></a>Configurare le impostazioni dell'account utente e del gruppo

Con la macchina virtuale aggiunta al dominio gestito di Azure AD DS e configurata per l'autenticazione, sono disponibili alcune opzioni di configurazione utente da completare. Queste modifiche alla configurazione includono consentire l'autenticazione basata su password e creare automaticamente Home directory nella macchina virtuale locale quando gli utenti di dominio hanno il primo accesso.

### <a name="allow-password-authentication-for-ssh"></a>Consenti l'autenticazione tramite password per SSH

Per impostazione predefinita, gli utenti possono accedere a una VM solo usando l'autenticazione basata su chiave pubblica SSH. L'autenticazione basata su password ha esito negativo. Quando si aggiunge la macchina virtuale a un dominio gestito Azure AD DS, gli account di dominio devono usare l'autenticazione basata su password. Aggiornare la configurazione SSH per consentire l'autenticazione basata su password, come indicato di seguito.

1. Aprire il file *sshd_conf* con un editor:

    ```console
    sudo vi /etc/ssh/sshd_config
    ```

1. Aggiornare la riga per *PasswordAuthentication* a *Sì*:

    ```console
    PasswordAuthentication yes
    ```

    Al termine, salvare e chiudere il file *sshd_conf* usando il `:wq` comando dell'editor.

1. Per applicare le modifiche e consentire agli utenti di accedere con una password, riavviare il servizio SSH:

    ```console
    sudo systemctl restart ssh
    ```

### <a name="configure-automatic-home-directory-creation"></a>Configurare la creazione automatica della home directory

Per abilitare la creazione automatica della Home Directory quando un utente accede per la prima volta, attenersi alla procedura seguente:

1. Aprire il file */etc/pam.d/common-session* in un editor:

    ```console
    sudo vi /etc/pam.d/common-session
    ```

1. Aggiungere la riga seguente nel file sotto la riga `session optional pam_sss.so`:

    ```console
    session required pam_mkhomedir.so skel=/etc/skel/ umask=0077
    ```

    Al termine, salvare e chiudere il file di *sessione comune* usando il `:wq` comando dell'editor.

### <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>Concedere i privilegi sudo al gruppo "Amministratori di AAD DC"

Per concedere ai membri degli *amministratori di AAD DC* i privilegi amministrativi per la VM Ubuntu, è necessario aggiungere una voce a */etc/sudoers*. Una volta aggiunti, i membri del gruppo *AAD DC Administrators* possono usare `sudo` il comando nella macchina virtuale Ubuntu.

1. Aprire il file *sudoers* per la modifica:

    ```console
    sudo visudo
    ```

1. Aggiungere la voce seguente alla fine del file */etc/sudoers* :

    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators ALL=(ALL) NOPASSWD:ALL
    ```

    Al termine, salvare e chiudere l'editor utilizzando il `Ctrl-X` comando.

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>Accedere alla macchina virtuale usando un account di dominio

Per verificare che la macchina virtuale sia stata aggiunta correttamente al dominio gestito di Azure AD DS, avviare una nuova connessione SSH usando un account utente di dominio. Verificare che sia stata creata una home directory e che sia stata applicata l'appartenenza al gruppo dal dominio.

1. Creare una nuova connessione SSH dalla console di. Usare un account di dominio appartenente al dominio gestito usando il `ssh -l` comando, `contosoadmin@contoso.com` ad esempio e quindi immettere l'indirizzo della macchina virtuale, ad esempio *Ubuntu.contoso.com*. Se si usa la Azure Cloud Shell, usare l'indirizzo IP pubblico della macchina virtuale anziché il nome DNS interno.

    ```console
    ssh -l contosoadmin@CONTOSO.com ubuntu.contoso.com
    ```

1. Dopo aver eseguito la connessione alla macchina virtuale, verificare che la home directory sia stata inizializzata correttamente:

    ```console
    pwd
    ```

    Si dovrebbe trovarsi nella directory */Home* con la propria directory che corrisponde all'account utente.

1. Verificare ora che le appartenenze ai gruppi siano state risolte correttamente:

    ```console
    id
    ```

    Le appartenenze ai gruppi verranno visualizzate dal dominio gestito di Azure AD DS.

1. Se è stato effettuato l'accesso alla macchina virtuale come membro del gruppo di *amministratori di AAD DC* , verificare che sia possibile usare `sudo` correttamente il comando:

    ```console
    sudo apt-get update
    ```

## <a name="next-steps"></a>Passaggi successivi

Se si verificano problemi durante la connessione della macchina virtuale al dominio gestito di Azure AD DS o l'accesso con un account di dominio, vedere [risoluzione dei](join-windows-vm.md#troubleshoot-domain-join-issues)problemi di aggiunta a un dominio.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
