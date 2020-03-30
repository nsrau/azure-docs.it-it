---
title: Aggiungere una macchina virtuale RHEL a Servizi di dominio Azure AD . Documenti Microsoft
description: Informazioni su come configurare e aggiungere una macchina virtuale Red Hat Enterprise Linux a un dominio gestito di Servizi di dominio Azure AD.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 16100caa-f209-4cb0-86d3-9e218aeb51c6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: iainfou
ms.openlocfilehash: d12dd0c79f2e9c1d2b0cc17956a0bb8d8fa35865
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78299143"
---
# <a name="join-a-red-hat-enterprise-linux-virtual-machine-to-an-azure-ad-domain-services-managed-domain"></a>Aggiungere una macchina virtuale Red Hat Enterprise Linux a un dominio gestito di Servizi di dominio Azure AD

Per consentire agli utenti di accedere alle macchine virtuali (VM) in Azure usando un singolo set di credenziali, è possibile aggiungere macchine virtuali a un dominio gestito di Servizi di dominio Azure Active Directory.To let users sign in to virtual machines (VMs) in Azure using a single set of credentials, you can join VMs to an Azure Active Directory Domain Services (AD DS) managed domain. Quando si aggiunge una macchina virtuale a un dominio gestito di Azure AD DS, è possibile usare gli account utente e le credenziali del dominio per accedere e gestire i server. Vengono applicate anche le appartenenze ai gruppi del dominio gestito di Servizi di dominio Active Directory di Azure per consentire di controllare l'accesso a file o servizi nella macchina virtuale.

Questo articolo illustra come aggiungere una macchina virtuale Red Hat Enterprise Linux (RHEL) a un dominio gestito di Azure AD DS.This article shows you how to join a Red Hat Enterprise Linux (RHEL) VM to an Azure AD DS managed domain.

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione, sono necessari i privilegi e le risorse seguenti:

* Una sottoscrizione di Azure attiva.
    * Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un tenant di Azure Active Directory associato alla sottoscrizione, sincronizzato con una directory locale o con una directory solo cloud.
    * Se necessario, [creare un tenant di Azure Active Directory][create-azure-ad-tenant] o [associare una sottoscrizione di Azure al proprio account][associate-azure-ad-tenant].
* Un dominio gestito di Azure Active Directory Domain Services abilitato e configurato nel tenant di Azure AD.
    * Se necessario, seguire la prima esercitazione per [creare e configurare un'istanza di Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Un account utente che fa parte del dominio gestito di Servizi di dominio Active Directory di Azure.A user account that's a part of the Azure AD DS managed domain.

## <a name="create-and-connect-to-a-rhel-linux-vm"></a>Creare e connettersi a una macchina virtuale Linux RHELCreate and connect to a RHEL Linux VM

Se si dispone di una macchina virtuale RHEL Linux esistente in Azure, connettersi a essa tramite SSH, quindi continuare con il passaggio successivo per [avviare la configurazione della macchina virtuale.](#configure-the-hosts-file)

Se è necessario creare una macchina virtuale RHEL Linux o si vuole creare una macchina virtuale di test da usare con questo articolo, è possibile usare uno dei metodi seguenti:If you need to create a RHEL Linux VM, or want to create a test VM for use with this article, you can use one of the following methods:

* [Portale di Azure](../virtual-machines/linux/quick-create-portal.md)
* [Interfaccia della riga di comando di AzureAzure](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

Quando si crea la macchina virtuale, prestare attenzione alle impostazioni della rete virtuale per assicurarsi che la macchina virtuale possa comunicare con il dominio gestito di Servizi di dominio Active Directory di Azure:When you create the VM, previene attenzione alle impostazioni della rete virtuale per assicurarsi che la macchina virtuale possa comunicare con il dominio gestito di Azure AD DS:When you create the VM, pre-attention to the virtual network settings to make sure that the VM can communicate

* Distribuire la macchina virtuale nella stessa rete virtuale con peered in cui è stato abilitato Servizi di dominio Azure AD.
* Distribuire la macchina virtuale in una subnet diversa rispetto all'istanza di Servizi di dominio Azure AD.

Dopo aver distribuito la macchina virtuale, seguire i passaggi per connettersi alla macchina virtuale usando SSH.

## <a name="configure-the-hosts-file"></a>Configurare il file hosts

Per assicurarsi che il nome host della macchina virtuale sia configurato correttamente per il dominio gestito, modificare il file */etc/hosts* e impostare il nome host:

```console
sudo vi /etc/hosts
```

Nel file *hosts,* aggiornare l'indirizzo *localhost.* Nell'esempio seguente:

* *aaddscontoso.com* è il nome di dominio DNS del dominio gestito di Servizi di dominio Active Directory di Azure.For is the DNS domain name of your Azure AD DS managed domain.
* *rhel* è il nome host della macchina virtuale RHEL che si sta aggiungendo al dominio gestito.

Aggiornare questi nomi con i propri valori:Update these names with your own values:

```console
127.0.0.1 rhel rhel.aaddscontoso.com
```

Al termine, salvare *hosts* e uscire `:wq` dal file hosts utilizzando il comando dell'editor.

## <a name="install-required-packages"></a>Installare i pacchetti necessari

La macchina virtuale richiede alcuni pacchetti aggiuntivi per aggiungere la macchina virtuale al dominio gestito di Servizi di dominio Active Directory di Azure.The VM needs some additional packages to join the VM to the Azure AD DS managed domain. Per installare e configurare questi pacchetti, aggiornare `yum`e installare gli strumenti di aggiunta al dominio utilizzando . Esistono alcune differenze tra RHEL 7.x e RHEL 6.x, quindi utilizzare i comandi appropriati per la versione distro nelle sezioni rimanenti di questo articolo.

**RHEL 7**

```console
sudo yum install realmd sssd krb5-workstation krb5-libs oddjob oddjob-mkhomedir samba-common-tools
```

**RHEL 6**

```console
sudo yum install adcli sssd authconfig krb5-workstation
```

## <a name="join-vm-to-the-managed-domain"></a>Aggiungere la macchina virtuale al dominio gestitoJoin VM to the managed domain

Ora che i pacchetti necessari sono installati nella macchina virtuale, aggiungere la macchina virtuale al dominio gestito di Azure AD DS.Now that the required packages are installed on the VM, join the VM to the Azure AD DS managed domain. Anche in questo caso, utilizzare la procedura appropriata per la versione distro RHEL.

### <a name="rhel-7"></a>RHEL 7

1. Usare `realm discover` il comando per individuare il dominio gestito di Servizi di dominio Active Directory di Azure.Use the command to discover the Azure AD DS managed domain. Nell'esempio seguente viene individuata l'area *di autenticazione AADDSCONTOSO.COM*. Specificare il proprio nome di dominio gestito di Azure AD DS in TUTTI MAIUSCOLE:Specify your own Azure AD DS managed domain name in ALL UPPERCASE:

    ```console
    sudo realm discover AADDSCONTOSO.COM
    ```

   Se `realm discover` il comando non riesce a trovare il dominio gestito di Azure AD DS, esaminare la procedura di risoluzione dei problemi seguente:If the command can't find your Azure AD DS managed domain, review the following troubleshooting steps:

    * Assicurarsi che il dominio sia raggiungibile dalla macchina virtuale. Prova `ping aaddscontoso.com` a vedere se viene restituita una risposta positiva.
    * Verificare che la macchina virtuale sia distribuita nella stessa rete virtuale di peered in cui è disponibile il dominio gestito di Servizi di dominio Active Directory di Azure.Check that the VM is deployed to the same, or a peered, virtual network in which the Azure AD DS managed domain is available.
    * Verificare che le impostazioni del server DNS per la rete virtuale siano state aggiornate in modo che puntino ai controller di dominio del dominio gestito di Servizi di dominio Active Directory di Azure.

1. Inizializzare ora `kinit` Kerberos utilizzando il comando . Specificare un utente che fa parte del dominio gestito di Servizi di dominio Active Directory di Azure.Specify a user that's a part of the Azure AD DS managed domain. Se necessario, [aggiungere un account utente a un gruppo in Azure AD.](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md)

    Anche in questo caso, il nome di dominio gestito di Azure AD DS deve essere immesso in TUTTI maiuscole. Nell'esempio seguente, l'account denominato `contosoadmin@aaddscontoso.com` viene utilizzato per inizializzare Kerberos. Immettere il proprio account utente che fa parte del dominio gestito di Servizi di dominio Active Directory di Azure:Enter your own user account that's a part of the Azure AD DS managed domain:

    ```console
    kinit contosoadmin@AADDSCONTOSO.COM
    ```

1. Infine, aggiungere il computer al dominio gestito `realm join` di Servizi di dominio Active Directory di Azure usando il comando. Usare lo stesso account utente che fa parte del dominio gestito di `kinit` Servizi di `contosoadmin@AADDSCONTOSO.COM`dominio Active Directory di Azure specificato nel comando precedente, ad esempio:

    ```console
    sudo realm join --verbose AADDSCONTOSO.COM -U 'contosoadmin@AADDSCONTOSO.COM'
    ```

L'aggiunta della macchina virtuale al dominio gestito di Servizi di dominio Active Directory di Azure richiede alcuni minuti. L'output di esempio seguente mostra che la macchina virtuale è stata aggiunta correttamente al dominio gestito di Servizi di dominio Active Directory di Azure:The following example output shows the VM has successfully joined to the Azure AD DS managed domain:

```output
Successfully enrolled machine in realm
```

### <a name="rhel-6"></a>RHEL 6

1. Usare `adcli info` il comando per individuare il dominio gestito di Servizi di dominio Active Directory di Azure.Use the command to discover the Azure AD DS managed domain. Nell'esempio seguente viene discoverta l'area *di ADDDSCONTOSO.COM*. Specificare il proprio nome di dominio gestito di Azure AD DS in TUTTI MAIUSCOLE:Specify your own Azure AD DS managed domain name in ALL UPPERCASE:

    ```console
    sudo adcli info aaddscontoso.com
    ```

   Se `adcli info` il comando non riesce a trovare il dominio gestito di Azure AD DS, esaminare la procedura di risoluzione dei problemi seguente:If the command can't find your Azure AD DS managed domain, review the following troubleshooting steps:

    * Assicurarsi che il dominio sia raggiungibile dalla macchina virtuale. Prova `ping aaddscontoso.com` a vedere se viene restituita una risposta positiva.
    * Verificare che la macchina virtuale sia distribuita nella stessa rete virtuale di peered in cui è disponibile il dominio gestito di Servizi di dominio Active Directory di Azure.Check that the VM is deployed to the same, or a peered, virtual network in which the Azure AD DS managed domain is available.
    * Verificare che le impostazioni del server DNS per la rete virtuale siano state aggiornate in modo che puntino ai controller di dominio del dominio gestito di Servizi di dominio Active Directory di Azure.

1. In primo luogo, `adcli join` aggiungere il dominio utilizzando il comando, questo comando creerà anche la scheda chiave per autenticare la macchina. Usare un account utente che fa parte del dominio gestito di Servizi di dominio Active Directory di Azure.Use a user account that's a part of the Azure AD DS managed domain.

    ```console
    sudo adcli join aaddscontoso.com -U contosoadmin
    ```

1. Configurare `/ect/krb5.conf` ora e `/etc/sssd/sssd.conf` creare i `aaddscontoso.com` file per l'utilizzo del dominio di Active Directory.
   Assicurati che `AADDSCONTOSO.COM` sia sostituito dal tuo nome di dominio:

    Aprire `/ect/krb5.conf` il file con un editor:

    ```console
    sudo vi /etc/krb5.conf
    ```

    Aggiornare `krb5.conf` il file in modo che corrisponda all'esempio seguente:

    ```console
    [logging]
     default = FILE:/var/log/krb5libs.log
     kdc = FILE:/var/log/krb5kdc.log
     admin_server = FILE:/var/log/kadmind.log
    
    [libdefaults]
     default_realm = AADDSCONTOSO.COM
     dns_lookup_realm = true
     dns_lookup_kdc = true
     ticket_lifetime = 24h
     renew_lifetime = 7d
     forwardable = true
    
    [realms]
     AADDSCONTOSO.COM = {
     kdc = AADDSCONTOSO.COM
     admin_server = AADDSCONTOSO.COM
     }
    
    [domain_realm]
     .AADDSCONTOSO.COM = AADDSCONTOSO.COM
     AADDSCONTOSO.COM = AADDSCONTOSO.COM
    ```
    
   Creare `/etc/sssd/sssd.conf` il file:
    
    ```console
    sudo vi /etc/sssd/sssd.conf
    ```

    Aggiornare `sssd.conf` il file in modo che corrisponda all'esempio seguente:

    ```console
    [sssd]
     services = nss, pam, ssh, autofs
     config_file_version = 2
     domains = AADDSCONTOSO.COM
    
    [domain/AADDSCONTOSO.COM]
    
     id_provider = ad
    ```

1. Assicurarsi `/etc/sssd/sssd.conf` che le autorizzazioni siano 600 e siano di proprietà dell'utente root:

    ```console
    sudo chmod 600 /etc/sssd/sssd.conf
    sudo chown root:root /etc/sssd/sssd.conf
    ```

1. Usare `authconfig` per indicare alla macchina virtuale l'integrazione di AD Linux:Use to is struct the VM about the AD Linux integration:

    ```console
    sudo authconfig --enablesssd --enablesssdauth --update
    ```

1. Avviare e abilitare il servizio sssd:

    ```console
    sudo service sssd start
    sudo chkconfig sssd on
    ```

Se la macchina virtuale non riesce a completare correttamente il processo di aggiunta al dominio, assicurarsi che il gruppo di sicurezza di rete della macchina virtuale consenta il traffico Kerberos in uscita sulla porta TCP -UDP 464 alla subnet di rete virtuale per il dominio gestito di Azure AD DS.

Ora controllare se è possibile interrogare le informazioni utente di Active Directory utilizzando`getent`

```console
sudo getent passwd contosoadmin
```

## <a name="allow-password-authentication-for-ssh"></a>Consenti autenticazione password per SSH

Per impostazione predefinita, gli utenti possono accedere a una macchina virtuale solo usando l'autenticazione basata su chiave pubblica SSH.By default, users can only sign in to a VM using SSH public key-based authentication. L'autenticazione basata su password ha esito negativo. Quando si aggiunge la macchina virtuale a un dominio gestito di Azure AD DS, tali account di dominio devono usare l'autenticazione basata su password. Aggiornare la configurazione SSH per consentire l'autenticazione basata su password come indicato di seguito.

1. Aprire il file *sshd_conf* con un editor:

    ```console
    sudo vi /etc/ssh/sshd_config
    ```

1. Aggiornare la riga per *PasswordAuthentication* su *yes*:

    ```console
    PasswordAuthentication yes
    ```

    Al termine, salvare e uscire `:wq` dal file *sshd_conf* utilizzando il comando dell'editor.

1. Per applicare le modifiche e consentire agli utenti di accedere con una password, riavviare il servizio SSH per la versione distro RHEL:

   **RHEL 7**

    ```console
    sudo systemctl restart sshd
    ```

   **RHEL 6**

    ```console
    sudo service sshd restart
    ```

## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>Concedere i privilegi sudo al gruppo "Amministratori di AAD DC"

Per concedere ai membri del gruppo *AAD DC Administrators* privilegi amministrativi sulla macchina virtuale RHEL, aggiungere una voce al */etc/sudoers*. Una volta aggiunti, i membri del gruppo `sudo` *AAD DC Administrators* possono utilizzare il comando nella macchina virtuale RHEL.

1. Aprire il file *sudoers* per la modifica:

    ```console
    sudo visudo
    ```

1. Aggiungere la seguente voce alla fine del file */etc/sudoers.* Il gruppo *AAD DC Administrators* contiene spazi vuoti nel nome, quindi includere il carattere di escape della barra rovesciata nel nome del gruppo. Aggiungere il proprio nome di dominio, ad esempio *aaddscontoso.com:*

    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators@aaddscontoso.com ALL=(ALL) NOPASSWD:ALL
    ```

    Al termine, salvare e uscire `:wq` dall'editor utilizzando il comando dell'editor.

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>Accedere alla macchina virtuale usando un account di dominioSign in to the VM using a domain account

Per verificare che la macchina virtuale sia stata aggiunta correttamente al dominio gestito di Servizi di dominio Active Directory di Azure, avviare una nuova connessione SSH usando un account utente di dominio. Verificare che sia stata creata una home directory e che venga applicata l'appartenenza al gruppo dal dominio.

1. Creare una nuova connessione SSH dalla console. Usare un account di dominio che `ssh -l` appartiene al `contosoadmin@aaddscontoso.com` dominio gestito utilizzando il comando , ad esempio e quindi immettere l'indirizzo della macchina virtuale, ad esempio *rhel.aaddscontoso.com*. Se si usa Azure Cloud Shell, usare l'indirizzo IP pubblico della macchina virtuale anziché il nome DNS interno.

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com rhel.aaddscontoso.com
    ```

1. Dopo aver eseguito correttamente la connessione alla macchina virtuale, verificare che la home directory sia stata inizializzata correttamente:When you've successfully connected to the VM, verify that the home directory was initialized correctly:

    ```console
    pwd
    ```

    È necessario trovarsi nella directory */home* con la propria directory che corrisponde all'account utente.

1. Verificare ora che le appartenenze ai gruppi vengano risolte correttamente:

    ```console
    id
    ```

    Dovrebbero essere visualizzate le appartenenze ai gruppi dal dominio gestito di Servizi di dominio Active Directory di Azure.You should see your group memberships from the Azure AD DS managed domain.

1. Se è stato eseguito l'accesso alla macchina virtuale come membro del `sudo` gruppo *AAD DC Administrators,* verificare che sia possibile utilizzare correttamente il comando:

    ```console
    sudo yum update
    ```

## <a name="next-steps"></a>Passaggi successivi

In caso di problemi durante la connessione della macchina virtuale al dominio gestito di Servizi di dominio Active Directory di Azure o l'accesso con un account di dominio, vedere [Risoluzione dei problemi](join-windows-vm.md#troubleshoot-domain-join-issues)relativi all'aggiunta a un dominio.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
